---
layout: post
title:  "AVFoundation 前置摄像头"
date:   2023-08-17 11:08:59 +0800
categories: gpt book ios
---

## 在iOS端采集前置摄像头视频并显示在UIView上

可以使用AVFoundation框架来实现。下面是一个简单的步骤指南：

1. 导入AVFoundation框架：在您的Xcode项目中，确保导入了AVFoundation框架。

2. 设置权限：前置摄像头需要获得用户的授权才能使用。在项目的Info.plist文件中添加以下键值对，以请求相机访问权限：
   ```
   Privacy - Camera Usage Description : 我们需要访问您的相机以采集视频。
   ```

3. 创建会话和输入设备：使用AVCaptureSession创建一个会话对象，并创建一个AVCaptureDevice实例来表示前置摄像头。您可以使用`AVCaptureDevice.DiscoverySession`来查找可用的摄像头设备。

4. 创建输入设备对象：使用`AVCaptureDeviceInput`来创建一个输入设备对象，将前置摄像头设备作为参数传入。

5. 创建输出对象：使用`AVCaptureVideoDataOutput`来创建一个输出对象，用于从摄像头获取视频帧。

6. 设置预览图层：创建一个`AVCaptureVideoPreviewLayer`实例，将会话对象设置为图层的会话，并将图层添加到您的UIView上。

7. 启动会话：使用`startRunning()`方法启动会话，开始采集视频。

下面是一个简单的示例代码，演示了如何实现这些步骤：

```swift
import AVFoundation
import UIKit

class ViewController: UIViewController {

    let captureSession = AVCaptureSession()
    var videoPreviewLayer: AVCaptureVideoPreviewLayer?

    override func viewDidLoad() {
        super.viewDidLoad()

        // 创建会话和输入设备
        guard let captureDevice = AVCaptureDevice.default(.builtInWideAngleCamera, for: .video, position: .front),
              let deviceInput = try? AVCaptureDeviceInput(device: captureDevice)
        else {
            fatalError("无法访问前置摄像头")
        }

        // 添加输入设备到会话
        if captureSession.canAddInput(deviceInput) {
            captureSession.addInput(deviceInput)
        }

        // 创建输出对象
        let videoOutput = AVCaptureVideoDataOutput()
        videoOutput.setSampleBufferDelegate(self, queue: DispatchQueue(label: "videoQueue"))

        // 添加输出对象到会话
        if captureSession.canAddOutput(videoOutput) {
            captureSession.addOutput(videoOutput)
        }

        // 设置预览图层
        videoPreviewLayer = AVCaptureVideoPreviewLayer(session: captureSession)
        videoPreviewLayer?.videoGravity = AVLayerVideoGravity.resizeAspectFill
        videoPreviewLayer?.frame = view.layer.bounds
        if let previewLayer = videoPreviewLayer {
            view.layer.addSublayer(previewLayer)
        }

        // 启动会话
        captureSession.startRunning()
    }
}

extension ViewController: AVCaptureVideoDataOutputSampleBufferDelegate {
    func captureOutput(_ output: AVCaptureOutput, didOutput sampleBuffer: CMSampleBuffer, from connection: AVCaptureConnection) {
        // 在这里处理视频帧数据
        // sampleBuffer 包含了视频帧的数据，您可以进行处理或显示在UIView上
    }
}
```
## 获取每一帧

这是一个基本的示例，您可以根据需要进行调整和扩展。通过处理`captureOutput(_:didOutput:from:)`方法中的`sampleBuffer`，您可以对视频帧进行进一步的处理或在UIView上显示示。

在`captureOutput(_:didOutput:from:)`方法中，您可以处理视频帧数据。以下是一个示例实现，展示了如何获取视频帧的图像数据并在UIView上显示：

```swift
extension ViewController: AVCaptureVideoDataOutputSampleBufferDelegate {
    func captureOutput(_ output: AVCaptureOutput, didOutput sampleBuffer: CMSampleBuffer, from connection: AVCaptureConnection) {
        // 将sampleBuffer转换为UIImage
        guard let imageBuffer = CMSampleBufferGetImageBuffer(sampleBuffer) else {
            return
        }
        let ciImage = CIImage(cvImageBuffer: imageBuffer)
        let context = CIContext()
        guard let cgImage = context.createCGImage(ciImage, from: ciImage.extent) else {
            return
        }
        let image = UIImage(cgImage: cgImage)

        // 在主线程更新UI
        DispatchQueue.main.async {
            // 将图像显示在UIView上
            self.imageView.image = image
        }
    }
}
```

在这个示例中，我们首先将`sampleBuffer`转换为`CIImage`，然后使用`CIContext`将其转换为`CGImage`，最后创建`UIImage`对象。然后，我们在主线程中更新UI，将图像显示在名为`imageView`的UIImageView上。

请确保在您的视图控制器中添加一个名为`imageView`的UIImageView，并将其与代码中的`self.imageView`关联起来。

这只是一个简单的示例，您可以根据需要进行进一步的图像处理或在UIView上显示。

```swift
let videoQueue = DispatchQueue(label: "videoQueue")
videoOutput.setSampleBufferDelegate(self, queue: videoQueue)

videoQueue.async {
    self.captureSession.startRunning()
}
```
