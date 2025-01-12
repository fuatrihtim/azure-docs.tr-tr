---
title: .NET kullanarak Media Encoder Standard bir varlık kodlayın | Microsoft Docs
description: Bu makalede, Media Encoder Standard kullanarak bir varlığı kodlamak için .NET kullanımı gösterilmektedir.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 03431b64-5518-478a-a1c2-1de345999274
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.reviewer: anilmur
ms.custom: devx-track-csharp
ms.openlocfilehash: b94ec0b50182381d2a4e1bca09f4c8bc482d13ee
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103014103"
---
# <a name="encode-an-asset-with-media-encoder-standard-using-net"></a>.NET kullanarak Media Encoder Standard bir varlık kodla

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]  

Kodlama işleri, Media Services en yaygın işlem işlemlerinden biridir. Bir kodlamadan diğerine medya dosyalarını dönüştürmek için kodlama işleri oluşturursunuz. Kodlarken, yerleşik Medya Kodlayıcısı Media Services kullanabilirsiniz. Bir Media Services iş ortağı tarafından sunulan bir kodlayıcı de kullanabilirsiniz; üçüncü taraf kodlayıcılar Azure Marketi aracılığıyla kullanılabilir. 

Bu makalede, varlıklarınızı Media Encoder Standard (MES) ile kodlamak için .NET kullanımı gösterilmektedir. Media Encoder Standard, [burada](./media-services-mes-presets-overview.md)açıklanan kodlayıcılar önayarlarından biri kullanılarak yapılandırılır.

Kaynak dosyalarınızı her zaman Uyarlamalı bit hızı bir MP4 kümesine kodlamak ve sonra [dinamik paketleme](media-services-dynamic-packaging-overview.md)kullanarak kümeyi istenen biçime dönüştürmeniz önerilir. 

Çıkış varlığınız depolama şifreli ise, varlık teslim ilkesini yapılandırmanız gerekir. Daha fazla bilgi için bkz. [varlık teslim Ilkesini yapılandırma](media-services-dotnet-configure-asset-delivery-policy.md).

> [!NOTE]
> MES, giriş dosyası adının ilk 32 karakterini içeren bir çıkış dosyası oluşturur. Ad, önceden ayarlanan dosyada belirtime göre belirlenir. Örneğin, "FileName": "{baseName} _ {Index} {Extension}". {BaseName}, giriş dosyası adının ilk 32 karakteriyle değiştirilmiştir.
> 
> 

### <a name="mes-formats"></a>MES biçimleri
[Biçimler ve codec bileşenleri](media-services-media-encoder-standard-formats.md)

### <a name="mes-presets"></a>MES Ön ayarları
Media Encoder Standard, [burada](./media-services-mes-presets-overview.md)açıklanan kodlayıcılar önayarlarından biri kullanılarak yapılandırılır.

### <a name="input-and-output-metadata"></a>Giriş ve çıkış meta verileri
MES kullanarak bir giriş varlığını (veya varlıkları) kodlarken, bu kodlama görevinin başarıyla tamamlanmasında bir çıkış varlığı alırsınız. Çıktı varlığı, kullandığınız kodlama ön ayarını temel alarak video, ses, küçük resim, bildirim vb. içerir.

Çıktı varlığı, giriş varlığı hakkında meta veriler içeren bir dosya da içerir. Meta veri XML dosyasının adı şu biçimdedir: <asset_id # C1_metadata.xml (örneğin, 41114ad3-eb5e-4c57-8d92-5354e2b7d4a4_metadata.xml); burada <Asset_id>, giriş varlığının AssetId değeridir. Bu giriş meta veri XML şeması [burada](media-services-input-metadata-schema.md)açıklanmıştır.

Çıktı varlığı Ayrıca çıkış varlığı hakkında meta veriler içeren bir dosya içerir. Meta veri XML dosyasının adı şu biçimdedir: <source_file_name # C1_manifest.xml (örneğin, BigBuckBunny_manifest.xml). Bu çıktı meta veri XML şeması [burada](media-services-output-metadata-schema.md)açıklanmıştır.

İki meta veri dosyasından birini incelemek istiyorsanız SAS Konumlandırıcı oluşturabilir ve dosyayı yerel bilgisayarınıza indirebilirsiniz. SAS Konumlandırıcı oluşturma ve Media Services .NET SDK uzantılarını kullanarak bir dosyayı indirme hakkında bir örnek bulabilirsiniz.

## <a name="download-sample"></a>Örnek indirme
[Buradan](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/)nasıl kodlayacağınızı gösteren bir örnek alabilir ve çalıştırabilirsiniz.

## <a name="net-sample-code"></a>.NET örnek kodu

Aşağıdaki kod örneği aşağıdaki görevleri gerçekleştirmek için Media Services .NET SDK kullanır:

* Bir kodlama işi oluşturun.
* Media Encoder Standard Kodlayıcısı için bir başvuru alın.
* [Uyarlamalı akış](media-services-autogen-bitrate-ladder-with-mes.md) önceden ayarının kullanılacağını belirtin. 
* İşe tek bir kodlama görevi ekleyin. 
* Kodlanacak giriş varlığını belirtin.
* Kodlanmış varlığı içeren bir çıkış varlığı oluşturun.
* İşin ilerlemesini denetlemek için bir olay işleyicisi ekleyin.
* İşi gönder.

#### <a name="create-and-configure-a-visual-studio-project"></a>Visual Studio projesi oluşturup yapılandırma

Geliştirme ortamınızı kurun ve app.config dosyanızı [.NET ile Media Services geliştirme](media-services-dotnet-how-to-use.md) bölümünde açıklandığı gibi bağlantı bilgileriyle doldurun. 

#### <a name="example"></a>Örnek 

```csharp
using System;
using System.Linq;
using System.Configuration;
using System.IO;
using System.Threading;
using Microsoft.WindowsAzure.MediaServices.Client;

namespace MediaEncoderStandardSample
{
    class Program
    {
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        // Field for service context.
        private static CloudMediaContext _context = null;

        private static readonly string _supportFiles =
            Path.GetFullPath(@"../..\Media");

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);


            // Get an uploaded asset.
            var asset = _context.Assets.FirstOrDefault();

            // Encode and generate the output using the "Adaptive Streaming" preset.
            EncodeToAdaptiveBitrateMP4Set(asset);

            Console.ReadLine();
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Job");
            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Create a task with the encoding details, using a string preset.
            // In this case "Adaptive Streaming" preset is used.
            ITask task = job.Tasks.AddNew("My encoding task",
                processor,
                "Adaptive Streaming",
                TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);
            // Add an output asset to contain the results of the job. 
            // This output is specified as AssetCreationOptions.None, which 
            // means the output asset is not encrypted. 
            task.OutputAssets.AddNew("Output asset",
                AssetCreationOptions.None);

            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();

            return job.OutputMediaAssets[0];
        }

        private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);
            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
                    break;
                case JobState.Canceling:
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    Console.WriteLine("Please wait...\n");
                    break;
                case JobState.Canceled:
                case JobState.Error:

                    // Cast sender as a job.
                    IJob job = (IJob)sender;

                    // Display or log error details as needed.
                    break;
                default:
                    break;
            }
        }

        private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

            return processor;
        }
    }
}
```

## <a name="advanced-encoding-features-to-explore"></a>Araştırılacak gelişmiş kodlama özellikleri
* [Küçük resim oluşturma](media-services-dotnet-generate-thumbnail-with-mes.md)
* [Kodlama sırasında küçük resimler oluşturma](media-services-dotnet-generate-thumbnail-with-mes.md#example-of-generating-a-thumbnail-while-encoding)
* [Kodlama sırasında videoları kırpın](media-services-crop-video.md)
* [Kodlama ön ayarlarını özelleştirme](media-services-custom-mes-presets-with-dotnet.md)
* [Görüntü ile video paylaşma veya filigran](media-services-advanced-encoding-with-mes.md#overlay)

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Sonraki adımlar
.NET ile Media Encoder Standard [kullanarak küçük resim oluşturma](media-services-dotnet-generate-thumbnail-with-mes.md) 
 [Media Services kodlamaya genel bakış](media-services-encode-asset.md)