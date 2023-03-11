---
title: "Lambda Access Secure String on Systems Manager Parameter Store"
slug: "lambda-access-secure-string-systems-manager"
description: "Lambda Access Secure String on Systems Manager Parameter Store"
date: 2023-02-19T22:45:29+01:00
draft: false
editURL: https://github.com/Kmelow/dotslashrun/issues
tags: ["AWS", "CDK", "Lambda", "SSM", "KMS", "Node"]
---

![schematics](./schematics.svg)

This is how to access [secure strings](https://eu-west-1.console.aws.amazon.com/systems-manager/parameters) stored on the parameter store using a Lambda function using a CDK.

## Setup

### KMS Key

Retrieve or create a [KMS](https://eu-west-1.console.aws.amazon.com/kms) (Key Management Service), then take note of its arn.

1. Go to [KMS Console](https://eu-west-1.console.aws.amazon.com/kms)
2. Access Customer-managed keys
3. Click on Create key
4. Chose a symmetric key for encrypt and decrypt
5. Add alias and finalize the creation

### Secure String on SSM Parameter Store

Store a `SecureString` on SSM Parameter Store using the AWS Console or using the [put-parameter](https://docs.aws.amazon.com/cli/latest/reference/ssm/put-parameter.html) CLI.
Take note of the parameter's name.

1. Go to [SSM](https://eu-west-1.console.aws.amazon.com/systems-manager)
2. Access Parameter Store
3. Click on Create a parameter
4. Give it a name, chose type **SecureString** and chose the KMS key for encryption
5. Give it a value and finalize the creation

## CDK

You need to have already a [CDK bootstrapped](https://docs.aws.amazon.com/cdk/v2/guide/getting_started.html) in order to deploy changes to the account.

Installations:

```bash
npm i aws-cdk-lib/aws-kms aws-cdk-lib/aws-ssm
```

Create resources using the CDK:

```javascript
// cdkProject/lib/handlerAccessSecureStringStack/index.ts

import { Key } from 'aws-cdk-lib/aws-kms';

export class HandlerAccessSecureString extends Stack {
  constructor(scope: Construct, id: string, props?: StackProps) {
    super(scope, id, props);

    // 👇 Create a reference for the key
    const key = Key.fromKeyArn(
      this,
      'externalKey',
      '<KMS_ARN>',
    );

    // 👇 Create a reference for the parameter stored
    const ssm = StringParameter.fromSecureStringParameterAttributes(
      this,
      'ssmParams',
      { parameterName: '<PARAMETER_NAME>' },
    )

    // 👇 Handler that accesses the parameters
    const handler = new NodejsFunction(this, 'functionName', {
      runtime: Runtime.NODEJS_18_X,
      functionName: 'functionName',
      entry: 'lambda/index.ts',
      environment: {
        // 👇 pass the parameter name as env var
        ssmParamName: '<PARAMETER_NAME>', 
      },
    });
    // 👇 Allow Lambda to use key
    key.grantDecrypt(handler); 
    // 👇 Allow Lambda to access parameters on SSM
    ssm.forEach(ssmParam => ssmParam.grantRead(handler)); 
  }
}
```

Finally, use the [AWS SSM SDK](https://docs.aws.amazon.com/AWSJavaScriptSDK/v3/latest/clients/client-ssm/classes/getparametercommand.html) `GetParameterCommand` to access the parameter store

```typescript
// cdkProject/lib/handlerAccessSecureStringStack/lambda/index.ts

import {
  GetParameterCommand,
  SSMClient
} from '@aws-sdk/client-ssm';

let ssmClient = new SSMClient({
  region: process.env.REGION
});

interface EventProps {
  ssmParamName: string;
}

export const handler = async (event: EventProps) => {
  const { ssmParamName } = event;

  if (!ssmClient) {
    ssmClient = new SSMClient({ region: process.env.REGION });
  }

  try {
    // 👇 Retrieve API from SSM parameter
    const storedParameter = await ssmClient.send(
      new GetParameterCommand({
        Name: ssmParamName,
        // 👇 essential if `SecureString`
        WithDecryption: true, 
      }),
    );

    // 👇 Check
    if (
      storedParameter.$metadata.httpStatusCode !== 200 ||
      !storedParameter.Parameter ||
      typeof storedParameter.Parameter.Value !== 'string'
    ) {
      return Promise.reject('failed to retrieve');
    }

    // 👇 Use parameter
    const storedParameterValue = storedParameter.Parameter.Value;
  } catch (error) {
    console.error(error)
  }
}
```

## Outro

Want to [report a 🐛 bug](https://github.com/Kmelow/dotslashrun).

## References

- [AWS CDK](https://docs.aws.amazon.com/cdk/v2/guide/getting_started.html)
- [AWS SSM SDK](https://docs.aws.amazon.com/AWSJavaScriptSDK/v3/latest/clients/client-ssm/classes/getparametercommand.html)
- [AWS Console - secure strings](https://eu-west-1.console.aws.amazon.com/systems-manager/parameters)
- [AWS Console - KMS](https://eu-west-1.console.aws.amazon.com/kms)
 
