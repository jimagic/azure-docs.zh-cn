---
title: 快速入门：识别语音，.NET Framework (Windows) - 语音服务
titleSuffix: Azure Cognitive Services
description: 按照本指南使用适用于 Windows 的 .NET framework 和语音 SDK 创建语音转文本控制台应用程序。 完成后，可以使用计算机的麦克风实时将语音转录为文本。
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 12/13/2018
ms.author: wolfma
ms.openlocfilehash: f5b33f0710070ff71ab52a1798773fe97965ef34
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2018
ms.locfileid: "53719058"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-net-framework-windows"></a>快速入门：使用适用于 .NET Framework 的语音 SDK (Windows) 识别语音

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

按照本指南使用适用于 Windows 的 .NET framework 和语音 SDK 创建语音转文本控制台应用程序。 完成后，可以使用计算机的麦克风实时将语音转录为文本。

## <a name="prerequisites"></a>先决条件

若要完成本项目，需要：

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* 语音服务的订阅密钥。 [免费获得一个](get-started.md)。
* 能够访问计算机的麦克风

## <a name="create-a-visual-studio-project"></a>创建 Visual Studio 项目

[!INCLUDE [Create project ](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>添加示例代码

1. 打开 `Program.cs` 并使用此示例替换自动生成的代码：

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. 找到字符串 `YourSubscriptionKey` 并将其替换为你的语音服务订阅密钥。

1. 找到字符串 `YourServiceRegion` 并将其替换为与订阅关联的[区域](regions.md)。 例如，如果使用的是免费试用版，区域是 `westus`。

1. 保存对项目的更改。

## <a name="build-and-run-the-app"></a>生成并运行应用

1. 从菜单栏中，选择“构建” > “构建解决方案”。 现在，编译代码时应不会提示错误。

    ![Visual Studio 应用程序的屏幕截图，其中突出显示了“生成解决方案”选项](media/sdk/qs-csharp-dotnet-windows-08-build.png "成功生成")

1. 在菜单栏中，选择“调试” > “开始调试”，或按 F5 以启动应用程序。

    ![Visual Studio 应用程序的屏幕截图，其中突出显示了“启动调试”选项](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "启动应用进入调试")

1. 此时将显示控制台窗口，提示你说话。 现在，用英语说些内容。 你的语音将传输到语音服务并实时转录为文本。 结果将输出到控制台。

    ![成功识别后的控制台输出的屏幕截图](media/sdk/qs-csharp-dotnet-windows-10-console-output.png "成功识别后的控制台输出")

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [浏览 GitHub 上的 C# 示例](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>另请参阅

- [自定义声学模型](how-to-customize-acoustic-models.md)
- [自定义语言模型](how-to-customize-language-model.md)
