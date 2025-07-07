---
title: "Better Return Types in Java with Sealed Interfaces"
date: 2025-07-06
draft: false
tags: ["java", "sealed interfaces", "functional design", "clean code"]
description: "Why sealed interfaces are a better alternative to if-else chains when returning multiple outcomes from a function."
---

In many real-world applications, a function doesn’t return just one thing.

I bet this is common for you: you query user data by ID, and three things can happen:

1. ✅ You get the data  
2. 🔒 The user exists but restricts access (whatever really)
3. ❌ The user doesn't exist at all  

***

### The Problem with Typical Return Structures

Instead of using clean types, people often end up writing brittle logic like this:

```java
if (result.isError() && result.reason == "validation") {
    // handle validation error
} else if (!result.isError()) {
    // process valid result
} else if (result.errorType == NOT_FOUND) {
    // handle not found
}
```

This kind of code becomes unmaintainable and easy to break.

### Sealed Interfaces to the Rescue

Here’s how I rewrote it using Java 17+ features:

```java
public sealed interface UserDataResult permits UserData, UserNotFound, UserRestricted {}

public record UserData(Data data) implements UserDataResult {}
public record UserNotFound(String userId) implements UserDataResult {}
public record UserRestricted(String userId) implements UserDataResult {}
```

Now your function can return one of these types:

```java
public UserDataResult getDataForUser(String userId) { ... }
```

And handling the result becomes explicit and safe:

```java
switch (result) {
    case UserData data       -> show(data.data());
    case UserNotFound nf     -> log("User not found: " + nf.userId());
    case UserRestricted ur   -> warn("Access denied for user: " + ur.userId());
}
```

> 📎 [View the working example on GitHub](https://github.com/vshneer/javaMisc/blob/main/src/test/java/com/shnaier/javaMisc/misc/sealedTypes/SealedTypesTest.java)

### Why I Prefer This Style

✅ All return paths are modeled explicitly  
✅ The compiler forces you to handle each case  
✅ No flag juggling, no null, no error-wrapping  
✅ More readable and future-proof