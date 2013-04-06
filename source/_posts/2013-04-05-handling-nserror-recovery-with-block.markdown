---
layout: post
title: "Handling NSError Recovery With Block"
date: 2013-04-05 20:46
comments: true
categories: [Foundation]
---

There are several scenarios where we have to provide users recovery options while the application encountered an error. However, creating a recoverable NSError is not a very pleasant experience. It's just tedious, so I wrapped up the creation process into one single API with block support.

This NSError category changed the process of creating a recoverable NSError from

```obj-c
   // Assume id recoveryDelegate exists
    NSMutableDictionary *info = [[NSMutableDictionary alloc] initWithCapacity:0];
    [info setObject:@"title" forKey:NSLocalizedDescriptionKey];
    [info setObject:@"suggestion" forKey:NSLocalizedRecoverySuggestionErrorKey];
    [info setObject:[NSArray arrayWithObjects:@"default", @"alternate", @"other option", nil]  forKey:NSLocalizedRecoveryOptionsErrorKey];
    [info setObject:recoveryDelegate forKey:NSRecoveryAttempterErrorKey];
    
    NSError *error = [NSError errorWithDomain:@"MyErrorDomain" code:0 userInfo:info];
```

```obj-c
// somewhere in recoveryDelegate's implementation file
#pragma mark NSErrorRecoveryAttempting Protocol

// This will be invoked while the error was presented with -[NSView presentError:modalForWindow:....]
- (void)attemptRecoveryFromError:(NSError *)error optionIndex:(NSUInteger)recoveryOptionIndex delegate:(id)delegate didRecoverSelector:(SEL)didRecoverSelector contextInfo:(void *)contextInfo
{
   // Do something here to recover the error based on the selected option
}

// This will be invoked while the error was presented with -[NSApp presentError:]
- (BOOL)attemptRecoveryFromError:(NSError *)error optionIndex:(NSUInteger)recoveryOptionIndex
{
   // Do something here to recover the error based on the selected option
    return YES;
}
```

to


```obj-c

   NSError *error = [NSError ot_errorWithTitle:@"title" 
                            recoverySuggestion:@"suggestion" 
                               recoveryOptions:[NSArray arrayWithObjects:@"default", @"alternate", @"other option", nil] 
                               recoveryHandler:^(NSUInteger optionIndex) {
       // Do something here to recover the error based on the selected option
   }];
    
```

You can obtain the source code [here](https://gist.github.com/smokyonion/5324727) or see below

<script src="https://gist.github.com/smokyonion/5324727.js"></script>