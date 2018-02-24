# Real world snapshots with ARKit and Xcode

## Introduction

Using an AR platform for the first time can be really intimidating (I've been there!), that's why I want to demystify using ARKit with Xcode.

The goal is making an app that takes pictures and hangs them in the place where they were taken (Similar to some art projects we've seen in class)

## First steps

Let's first open Xcode and create a new project based on the **Augmented Reality App** template. This template does the heavy lifting of setting up everything you need to get you started right away.

![](https://raw.githubusercontent.com/cruzdiego/arkit-demo/master/arkit-demo-step1.png)

Make sure to select **SceneKit** as *content technology* (the engine ARKit uses for rendering stuff. SceneKit is for 3D, SpriteKit for 2D and Metal is Apple's OpenGL equivalent)

![](https://raw.githubusercontent.com/cruzdiego/arkit-demo/master/arkit-demo-step2.png)

Now press the Play button and run the project on your device. You've just made an awesome AR ship app without even coding anything!

## Let's add some code

For making the app we want, we basically need 2 things:

1. Take a picture
2. Place it in the real world

Add this piece of code anywhere on **ViewController.swift** for doing just that:

```
@IBAction func takeAndPlacePicture(sender: Any) {
        //Validating the framework and camera is working
        guard let currentFrame = sceneView.session.currentFrame else {
            return
        }
        
        //1. Taking a picture
        let imagePlane = SCNPlane(width: sceneView.bounds.width / 6000, height: sceneView.bounds.height / 6000)
        imagePlane.firstMaterial?.diffuse.contents = sceneView.snapshot()
        imagePlane.firstMaterial?.lightingModel = .constant
        
        //2. Placing it on the screen (by default it's 0,0,0 )
        let planeNode = SCNNode(geometry: imagePlane)
        sceneView.scene.rootNode.addChildNode(planeNode)
        
        //2.5 Moving it to the camera's position
        planeNode.simdTransform = currentFrame.camera.transform
    }
```

## Plugging things together

We need to run that code every time we tap the screen. So now we need:

1. Identify a tap on the screen
2. Trigger the code 

On **Main.storyboard** find a *UITapGestureRecognizer* on the bottom right of the screen, and drag it to the screen's top bar.

![](https://raw.githubusercontent.com/cruzdiego/arkit-demo/master/arkit-demo-step3.png)

Select it and drag *Sent actions* to the top bar's first icon (which represents **ViewController.swift**). Select **takeAndPlacePicture** from the popup list. This means our function will run every time our *UITapGestureRecognizer* recognizes a tap.

![](https://raw.githubusercontent.com/cruzdiego/arkit-demo/master/arkit-demo-step4.png)
![](https://raw.githubusercontent.com/cruzdiego/arkit-demo/master/arkit-demo-step7.png)

Last step is telling the *UITapGestureRecognizer* where it needs to recognize taps on (since you could have many different views on your screen). Select the big blue view on top of your screen and *gestureRecognizers* from the right sidebar to our *UITapGestureRecognizer*'s icon on the screen top bar.

![](https://raw.githubusercontent.com/cruzdiego/arkit-demo/master/arkit-demo-step8.png)

## And that's it!

Run the code again (with your device in landscape) and try it out. It works!

## Next steps

We can tweak just a bit more our code for having a better experience.

#### Remove the 3D ship

The plane is awesome, but maybe you don't want it on your app. So let's take it out.

Go to **ship.scn**, select the ship and hit *Delete*. That's it.

#### Better placing the snapshots

The snapshots look great, but they might be too close to the camera. Go to our function and replace 2.5 step with this:

```
//2.5 Moving it 10cm away from the camera's position
        var translation = matrix_identity_float4x4
        translation.columns.3.z = -0.1
        planeNode.simdTransform = matrix_multiply(currentFrame.camera.transform, translation)
```

Run again and the snapshots should appear at a more convenient distance from your device.

## More resources

You can check out the complete project on my Github page:
https://github.com/cruzdiego/arkit-demo

This tutorial has been heavily based from Apple's own video session on ARKit:
https://developer.apple.com/videos/play/wwdc2017/602/

For more videos like that and even more examples and documentation, make sure to visit Apple's website:
https://developer.apple.com/arkit/
