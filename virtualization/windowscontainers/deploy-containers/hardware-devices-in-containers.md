---
title: Windows のコンテナーでデバイスを使う
description: Windows のコンテナーにどのようなデバイスのサポートが存在します。
keywords: docker、コンテナー、デバイス、ハードウェア
author: cwilhit
ms.openlocfilehash: 6397a5050ee0c7cb4b62dc935af4975d9ab6b3db
ms.sourcegitcommit: 1b6a244c3604e48c42c851e580e3b59e2384c91a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/17/2019
ms.locfileid: "9014519"
---
# <a name="devices-in-containers-on-windows"></a>Windows のコンテナーでデバイスを使う

既定では、Windows コンテナーはホスト デバイス Linux コンテナーと同じように、最低限のアクセスを与えられます。 メリット - または - にアクセスして、ホスト ハードウェア デバイスとの通信も必要である特定のワークロードがあります。 このガイドは、コンテナーではサポートされているデバイスと作業を開始する方法について説明します。

## <a name="requirements"></a>要件

- Windows を実行する必要があります 2019 以降のサーバーまたは Windows 10 Pro/Enterprise 年 2018年 10 月と更新
- コンテナー イメージのバージョンは、1809 以降である必要があります。
- コンテナーは、プロセス分離モードで実行されている Windows コンテナーである必要があります。
- デバイスで Windows の機能は、Docker デーモンに存在しているときにまだ存在しない Docker クライアント (この[取得要求](https://github.com/docker/cli/pull/1606)を追跡するを参照してください)。 [Interrim する必要があります moby ソースから[独自の docker 実行可能ファイルの作成](https://github.com/moby/moby/blob/master/docs/contributing/software-req-win.md)を回避としてします。 次の操作に慣れていない場合は、上にあるリンクされている現在価値が反映されるまで、この機能を試して、待機をお勧めします。

## <a name="run-a-container-with-a-device"></a>デバイスを使って、コンテナーを実行します。

デバイスを使用してコンテナーを起動するには、次のコマンドを使用します。

```shell
docker run --isolation=process --device="class/{interface class GUID}" mcr.microsoft.com/windows/servercore:1809
```

置き換える必要があります、`{interface class guid}`適切な[デバイス インターフェイス クラス GUID](https://docs.microsoft.com/en-us/windows-hardware/drivers/install/overview-of-device-interface-classes)に含まれている次のセクションでします。

複数のデバイスには、コンテナーを開始するには、次のコマンドを使用して、つなげて複数`--device`引数。

```shell
docker run --isolation=process --device="class/{interface class GUID}" --device="class/{interface class GUID}" mcr.microsoft.com/windows/servercore:1809
```

Windows では、すべてのデバイスの宣言を実装するインターフェイス クラスの一覧です。 Docker には、このコマンドを渡す] を要求されたクラスの実装として識別されるすべてのデバイスをコンテナーに組み込ますることを確認します。

つまり**ホストからデバイスを割り当てることが**できます。 代わりに、ホストがユーザーと共有しますコンテナーします。 同様に、クラス GUID を指定するため、そのコンテナーとその GUID を実装する_すべて_のデバイスが共有されます。

## <a name="what-devices-are-supported"></a>どのようなデバイスがサポートされています。

次のデバイス (およびクラス Guid インターフェイスがデバイス) はサポートされています。
  
<table border="1" style="background-color:FFFFCC;border-collapse:collapse;border:1px solid FFCC00;color:000000;width:75%" cellpadding="5" cellspacing="5">
<thead>
<tr valign="top">
<th><center>デバイスの種類</center></th>
<th><center>インターフェイス クラス GUID</center></th>
</tr>
</thead>
<tbody>
<tr valign="top">
<td><center>GPIO</center></td>
<td><center>916EF1CB-8426-468D-A6F7-9AE8076881B3</center></td>
</tr>
<tr valign="top">
<td><center>I2C バス</center></td>
<td><center>A11EE3C6-8421-4202-A3E7-B91FF90188E4</center></td>
</tr>
<tr valign="top">
<td><center>COM ポート</center></td>
<td><center>86E0D1E0-8089-11D0-9CE4-08003E301F73</center></td>
</tr>
<tr valign="top">
<td><center>[SPI バス</center></td>
<td><center>DCDE6AF9-6610-4285-828F-CAAF78C424CC</center></td>
</tr>
</tbody>
</table>

> [!TIP]
> 上記のデバイスでは、Windows コンテナーで現在サポートされている_のみ_デバイス。 その他のすべてのクラス Guid を渡すしようとすると、開始に失敗しますコンテナーが発生します。

## <a name="hyper-v-container-device-support"></a>HYPER-V コンテナー デバイスのサポート

デバイスの割り当てとデバイスの共有でサポートされていない HYPER-V 分離コンテナー今日します。

## <a name="linux-containers-on-windows-lcow-device-support"></a>Windows (LCOW) デバイスのサポートの Linux コンテナー

デバイスの割り当てとデバイスの共有でサポートされていない LCOW 今日します。