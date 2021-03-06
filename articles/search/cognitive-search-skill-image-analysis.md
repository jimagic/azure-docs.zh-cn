---
title: 图像分析认知搜索技能 - Azure 搜索
description: 在 Azure 搜索扩充管道中使用 ImageAnalysis 认知技能通过图像分析来提取语义文本。
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: fc8780c5b99ce98a55a6cb08cfaa6585e5a4e89a
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2018
ms.locfileid: "53313303"
---
#   <a name="image-analysis-cognitive-skill"></a>图像分析认知技能

图像分析技能根据图像内容提取一组丰富的可视特征。 例如，可从图像生成标题栏、生成标记或识别名人和地标。

> [!NOTE]
> 自 2018 年 12 月 21 日起，你可将认知服务资源与 Azure 搜索技能集进行关联。 这将使我们能够开始收取技能集执行的费用。 在此日期，我们还会开始将图像提取视为文档破解阶段的一部分进行计费。 我们将继续提供文档文本提取服务（不收取额外费用）。
>
> 内置技能的执行将按现有的[认知服务即用即付价格](https://azure.microsoft.com/pricing/details/cognitive-services/)进行计费。 图像提取费用将按预览版定价进行计费，详见 [Azure 搜索定价页面](https://go.microsoft.com/fwlink/?linkid=2042400)。 了解[详细信息](cognitive-search-attach-cognitive-services.md)。


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Vision.ImageAnalysisSkill 

## <a name="skill-parameters"></a>技能参数

参数区分大小写。

| 参数名称     | Description |
|--------------------|-------------|
| defaultLanguageCode   |  表示要返回的语言的字符串。 该服务以指定的语言返回识别结果。 如果未指定此参数，则默认值为“en”。 <br/><br/>支持的语言为： <br/>en - 英语（默认） <br/> zh - 简体中文|
|visualFeatures |   表示要返回的可视特征类型的一组字符串。 有效的可视特征类型包括：  <ul><li> categories - 根据认知服务[文档](https://docs.microsoft.com/azure/cognitive-services/computer-vision/category-taxonomy)中定义的分类对图像内容进行分类。</li><li> tags - 使用与图像内容相关字词的详细列表来标记图像。</li><li>Description - 用完整的英文句子描述图像内容。</li><li>Faces - 检测人脸是否存在。 如果存在，则生成位置、性别和年龄。</li><li> ImageType - 检测图像是剪贴画还是素描。</li><li>   Color - 确定主题色、主色以及图像是否为黑白。</li><li>Adult - 检测图片是否具有色情性质（描绘裸体或性行为）。 也检测性暗示内容。</li></ul> 可视特征的名称区分大小写。|
| 详细信息   | 表示要返回的特定于域的详细信息的一组字符串。 有效的可视特征类型包括： <ul><li>Celebrities - 识别在图像中检测到的名人。</li><li>Landmarks - 识别在图像中检测到的地标。</li></ul>
 |

## <a name="skill-inputs"></a>技能输入

| 输入名称      | 说明                                          |
|---------------|------------------------------------------------------|
| 图像         | 复杂类型。 当前仅适用于“/document/normalized_images”字段，当 ```imageAction``` 设置为 ```generateNormalizedImages``` 时由 Azure Blob 索引器生成。 请参阅[此示例](#sample-output)获取详细信息。|



##  <a name="sample-definition"></a>示例定义
```json
{
    "@odata.type": "#Microsoft.Skills.Vision.ImageAnalysisSkill",
    "context": "/document/normalized_images/*",
    "visualFeatures": [
        "Tags",
        "Faces",
        "Categories",
        "Adult",
        "Description",
        "ImageType",
        "Color"
    ],
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "image",
            "source": "/document/normalized_images/*"
        }
    ],
    "outputs": [
        {
            "name": "categories",
            "targetName": "myCategories"
        },
        {
            "name": "tags",
            "targetName": "myTags"
        },
        {
            "name": "description",
            "targetName": "myDescription"
        },
        {
            "name": "faces",
            "targetName": "myFaces"
        },
        {
            "name": "imageType",
            "targetName": "myImageType"
        },
        {
            "name": "color",
            "targetName": "myColor"
        },
        {
            "name": "adult",
            "targetName": "myAdultCategory"
        }
    ]
}
```

##  <a name="sample-input"></a>示例输入

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {                
                "image":  {
                               "data": "BASE64 ENCODED STRING OF A JPEG IMAGE",
                               "width": 500,
                               "height": 300,
                               "originalWidth": 5000,  
                               "originalHeight": 3000,
                               "rotationFromOriginal": 90,
                               "contentOffset": 500  
                           }
            }
        }
    ]
}
```


##  <a name="sample-output"></a>示例输出

```json
{
    "values": [
      {
        "recordId": "1",
            "data": {
                "categories": [
           {
                        "name": "abstract_",
                        "score": 0.00390625
                    },
                    {
                "name": "people_",
                        "score": 0.83984375,
                "detail": {
                            "celebrities": [
                                {
                                    "name": "Satya Nadella",
                                    "faceBoundingBox": {
                                        "left": 597,
                                        "top": 162,
                                        "width": 248,
                                        "height": 248
                                    },
                                    "confidence": 0.999028444
                                }
                            ],
                            "landmarks": [
                                {
                                    "name": "Forbidden City",
                                    "confidence": 0.9978346
                                }
                            ]
                        }
                    }
                ],
                "adult": {
                    "isAdultContent": false,
                    "isRacyContent": false,
                    "adultScore": 0.0934349000453949,
                    "racyScore": 0.068613491952419281
                },
                "tags": [
                    {
                        "name": "person",
                        "confidence": 0.98979085683822632
                    },
                    {
                        "name": "man",
                        "confidence": 0.94493889808654785
                    },
                    {
                        "name": "outdoor",
                        "confidence": 0.938492476940155
                    },
                    {
                        "name": "window",
                        "confidence": 0.89513939619064331
                    }
                ],
                "description": {
                    "tags": [
                        "person",
                        "man",
                        "outdoor",
                        "window",
                        "glasses"
                    ],
                    "captions": [
                        {
                            "text": "Satya Nadella sitting on a bench",
                            "confidence": 0.48293603002174407
                        }
                    ]
                },
                "requestId": "0dbec5ad-a3d3-4f7e-96b4-dfd57efe967d",
                "metadata": {
                    "width": 1500,
                    "height": 1000,
                    "format": "Jpeg"
                },
                "faces": [
                    {
                        "age": 44,
                        "gender": "Male",
                    "faceBoundingBox": {
                            "left": 593,
                            "top": 160,
                            "width": 250,
                            "height": 250
                        }
                    }
                ],
                "color": {
                    "dominantColorForeground": "Brown",
                    "dominantColorBackground": "Brown",
                    "dominantColors": [
                        "Brown",
                        "Black"
                    ],
                    "accentColor": "873B59",
                    "isBwImg": false
                    },
                "imageType": {
                    "clipArtType": 0,
                    "lineDrawingType": 0
                }
           }
      }
    ]
}
```


## <a name="error-cases"></a>错误案例
在以下错误案例中，未提取任何元素。

| 错误代码 | Description |
|------------|-------------|
| NotSupportedLanguage | 不支持提供的语言。 |
| InvalidImageUrl | 图片 URL 格式不正确或无法访问。|
| InvalidImageFormat | 输入数据不是有效的图像。 |
| InvalidImageSize | 输入的图像太大。 |
| NotSupportedVisualFeature  | 指定的特征类型无效。 |
| NotSupportedImage | 不受支持的图片，例如儿童色情内容。 |
| InvalidDetails | 不受支持的特定于域的模型。 |

## <a name="see-also"></a>另请参阅

+ [预定义技能](cognitive-search-predefined-skills.md)
+ [如何定义技能集](cognitive-search-defining-skillset.md)
+ [创建索引器 (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
