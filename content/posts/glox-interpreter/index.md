---
title: "Glox Interpreter"
slug: "Glox Interpreter"
description: "Glox Interpreter"
date: 2023-07-22T16:35:07+02:00
draft: true
editURL: https://github.com/Kmelow/dotslashrun/issues
tags: ["Golang", "Go"]
---

**Insert Lead paragraph here.**

# Contents

Concepts developed while following the book and creating glox:

- Custom Error handling

# Context

# Introduction

The steps required to implement an interpreter are:

1. [Scan](#scanning)
2. Tokenize
3. Parse
4. Build syntax tree
5. Analyse

## Parser

# Scanning

> It loops the entire program (or a REPL line), groups lexemes and outputs tokens.

## Lexemes

> Raw substrings

In the following line:

```go
var language = "glox";
```

The lexemes are the following: `var`, `language`, `=`, `"glox"`, `;`

## Tokens

> A lexeme that has a meaningfull representation in the language (keyword, operator, bit of punctuation or literals).

## Literals

> Lexemes for strings and numbers

# Go Implementation

Discussion of language features while implementing glox

## Enums (Tokens)

Go doesn't have support for `enum`.
Although, there are a few different methods to implement an `enum` behaviour.

### Custom type + iota + switch

The current best way to implement them is using [`const`](https://go.dev/ref/spec#Constants) and [`iota`](https://go.dev/ref/spec#Iota).

```go
// Token avoids opperation clashes with other possible `enum`s.
type Token int

// 
const (
	// Single-character tokens
	LEFT_PAREN Token = iota + 1
	RIGHT_PAREN
	LEFT_BRACKET
	RIGHT_BRACKET
	LEFT_BRACE
)

// String implements the 
func (t Token) String() string {
	switch t {
	case LEFT_PAREN:
		return "left_paren"
	case RIGHT_PAREN:
		return "right_paren"
	case LEFT_BRACKET:
		return "left_bracket"
	case RIGHT_BRACKET:
		return "right_bracket"
	}
	return "unknown"
}
```

### Assigned Constants

However, I think this is super verbose and error prone.
I decided to implement the token inspired by [http status codes](https://cs.opensource.google/go/go/+/go1.20.6:src/net/http/status.go;l=9)

### Go generate

Did not explored [go generate](https://go.dev/blog/generate), but it can be usefull to auto-generate based on `iota` values.

## Error Handling

Creating a custom error handler:

```go
// GloxError general error handling structure for the interpreter
type GloxError struct {
	Line       int
	StatusCode int
	Err        error
}

// Error implements the error interface
func (ge *GloxError) Error() string {
	return fmt.Sprintf("ERROR: [%d] - line [%d]\n\terr: %s", ge.StatusCode, ge.Line, ge.Err)
}
```

Then, using it as:

```go
return &GloxError{
  Line:       12,
  StatusCode: 404,
  Err:        "my custom error message",
}
```


# References