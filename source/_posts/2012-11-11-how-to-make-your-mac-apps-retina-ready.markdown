---
layout: post
title: "How to Make Your Mac Apps Retina-Ready"
date: 2012-11-11 11:58
comments: true
categories: [Cocoa, Retina, CALayer, NSView, Core Animation]
---

In the implementation file of your NSView class/subclass

```obj-c

#pragma mark Retina Display Support

- (void)_updateContentScale
{
    NSWindow *mainWindow = [NSApp mainWindow];
    NSWindow *layerWindow = [self window];
    if (mainWindow || layerWindow) {
        CGFloat scale = [(layerWindow != nil) ? layerWindow : mainWindow backingScaleFactor];
        CALayer *layer = self.layer;
        if ([layer respondsToSelector:@selector(contentsScale)]) {
            [self.layer setContentsScale:scale];
            // TODO: setContentsScale for other layers belonged to this view here….
        }
    }
}

- (void)scaleDidChange:(NSNotification *)n
{
    [self _updateContentScale];
}

- (void)viewDidMoveToWindow
{
    // Retina Display support
    [[NSNotificationCenter defaultCenter] addObserver:self
                                             selector:@selector(scaleDidChange:)
                                                 name:@"NSWindowDidChangeBackingPropertiesNotification"
                                               object:[self window]];
    
    // immediately update scale after the view has been added to a window
    [self _updateContentScale];
}

- (void)removeFromSuperview
{
    [super removeFromSuperview];
    [[NSNotificationCenter defaultCenter] removeObserver:self name:@"NSWindowDidChangeBackingPropertiesNotification" object:[self window]];
}

```
