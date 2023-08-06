---
title: "Next on Aws"
slug: "Next on Aws"
description: "Next on Aws"
date: 2023-02-18T16:35:26+01:00
draft: true
editURL: https://github.com/Kmelow/dotslashrun/issues
tags: ["AWS", "CDK", "Nextjs", "Amplify"]
---

> Deploy production ready [Nextjs](https://nextjs.org/) application to AWS using [CDK](https://aws.amazon.com/cdk/).
Points covered:

- What features AWS supports when deploying a Nextjs application
- How to use IaC (Infrastructure As Code) [CDK](https://aws.amazon.com/cdk/) (Cloud Development Kit) to manage the application lifecycle

## Context

Next.js is a full stack framework for web applications.
It contains a rich set of features for the front and backend as well as image optimisation, routing, etc.
There are different ways of deploying such application: 

- **DIY**: You launch an instance [EC2](https://aws.amazon.com/ec2/) and consider it a native Next.js experience.
You will have available all Next.js features at the cost of having to handle with all aspects considering infrastructure, such as: DNS, Authentication, Network, deployment (CI/CD).
- **Platform As A Service (PaaS)**: This method relies on using native AWS resources to handle your application code.
It provides you with a rich set of features and integrations with other AWS resources.
The cost of this method is that you depend on AWS integrations of Next.js features.

In the end the tradeoff is: full set of Next.js features against a complete app + infrastructure tooling.

The mission I chose to accept is to:

> Evaluate which Next.js features should I leave behind and which features AWS gives me while using [Amplify](https://aws.amazon.com/amplify/) (AWS's full-stack deployment resource).

This is the set of tools I'll be using:

- Next.js 13: React framework for creating full-stack web applications
- AWS Amplify: It is a set of tools and services to build full-stack web applications


## Next.js on AWS

AWS announced support for Next.js SSR web apps on May 18, 2021.
Next.js features available on AWS Amplify:

- Route pre-fetching
- SSR (prerendered request), SSG (prerendered at build time) and incremental Static Regeneration (ISR)
- API routes and dynamic routes
- Internationalization (i18n)
- Middleware
- Image optimisation
- Automatic pre-rendering
- [getStaticProps](https://nextjs.org/docs/basic-features/data-fetching/get-static-props)
- [getStatic](https://nextjs.org/docs/basic-features/data-fetching/get-static-paths)

What is not supported:

- Edge routes
- On-demand (ISR)


## Amplify

Amplify consists of 3 parts:

- Set of open source libraries and UI components with cloud-powered features
- A CLI toolchain
- Amplify console, to manage full-stack deployments

Amplify features added on top of Next.js application:

- Automatic branch based deployment
- Authentication with Cognito
- Password protection
- Session management
- As of November 17th, 2022 Amplify manages the server-side resources

## Configurations

The documentation on [Aplify Dev Center](https://docs.amplify.aws/lib/ssr/q/platform/js/#datastore) describes the implementation details when using SSR features with Amplify.

### When on the client

- In `_app.ts` we inform Amplify of the intent of using SSG with

```javascript
// pages/_app.js
import { Amplify } from "aws-amplify";
import awsExports from "../src/aws-exports";
Amplify.configure({ ...awsExports, ssr: true });
```

### When on the server:

```javascript
import { Amplify, API, withSSRContext } from "aws-amplify";

export async function getServerSideProps({ req }) {
  // Notice how the server uses `API` from `withSSRContext`, instead of the top-level `API`.
  const SSR = withSSRContext({ req }) // 👈
  const { data } = await SSR.API.graphql({ query: listPosts });

  return {
    props: {
      posts: data.listPosts.items
    }
  }
}
```

And also, when returning data from the server the Amplify method `serializeModel(jsObj)` should be used

```javascript
import { serializeModel } from '@aws-amplify/datastore/ssr';

// ...

export async function getServerSideProps({ req }) {
    const SSR = withSSRContext({ req });
    const posts = await SSR.DataStore.query(Post);

    return {
        props: {
      // This converts Post instances into serialized JSON for the client
      posts: serializeModel(posts), // 👈
        },
    };
}
```

## References

- [Next.js Workshop on AWS]: https://catalog.us-east-1.prod.workshops.aws/workshops/36375ad5-9091-4960-97dd-e82eb516f2c3/en-US
- Amplify Dev Center - [Getting Started with Server-Side Rendering (SSR)]: https://docs.amplify.aws/lib/ssr/q/platform/js/
- [Amplify UI library]: (https://ui.docs.amplify.aws/)
- [CDK Example]: (https://aws.amazon.com/blogs/mobile/deploy-a-nextjs-13-application-to-amplify-with-the-aws-cdk/)
- [SSR Example App]: (https://aws.amazon.com/blogs/mobile/ssr-support-for-aws-amplify-javascript-libraries/)
