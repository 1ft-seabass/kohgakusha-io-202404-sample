![02-unity-setting_03.png](images/02-unity-setting/02-unity-setting_03.png)

Hierarchy を確認します。

## EventSystem の配置

![02-unity-setting_12.png](images/02-unity-setting/02-unity-setting_12.png)

Hierarchy で上部の＋ボタンから UI > EventSystem を選択します。

![02-unity-setting_18.png](images/02-unity-setting/02-unity-setting_18.png)

EventSystem が配置されます。

## MainCamera に PhysicsRaycaster コンポーネントの配置

![02-unity-setting_08.png](images/02-unity-setting/02-unity-setting_08.png)

MainCamera をクリックします。

![02-unity-setting_15.png](images/02-unity-setting/02-unity-setting_15.png)

MainCamera を選択した状態で Inspector を確認して Add Component をクリックします。

![02-unity-setting_01.png](images/02-unity-setting/02-unity-setting_01.png)

コンポーネントの選択画面が出てきます。

![02-unity-setting_10.png](images/02-unity-setting/02-unity-setting_10.png)

検索エリアに PhysicsRaycaster を検索してクリックして選択します。

![02-unity-setting_11.png](images/02-unity-setting/02-unity-setting_11.png)

PhysicsRaycaster が追加されました。

## Cube の配置

![02-unity-setting_06.png](images/02-unity-setting/02-unity-setting_06.png)

Hierarchy で上部の＋ボタンから 3D Object > Cude の操作をして Cube オブジェクトを配置します。

![02-unity-setting_07.png](images/02-unity-setting/02-unity-setting_07.png)

Cube というオブジェクトが作成されます。

![02-unity-setting_17.png](images/02-unity-setting/02-unity-setting_17.png)

Cube を選択した状態で Inspector を確認して Add Component をクリックします。

![02-unity-setting_09.png](images/02-unity-setting/02-unity-setting_09.png)

New script をクリックします。

![02-unity-setting_14.png](images/02-unity-setting/02-unity-setting_14.png)

Name が聞かれるので NodeRedPostShiba と入力して Create and Add をクリックします。

![02-unity-setting_13.png](images/02-unity-setting/02-unity-setting_13.png)

コンポーネントが追加されたら、C# スクリプトを編集するために、こちらをダブルクリックします。

![02-unity-setting_16.png](images/02-unity-setting/02-unity-setting_16.png)

エディタが起動します。NodeRedPostShiba のスクリプトは以下を記述して保存します。

```csharp
using UnityEngine;
using UnityEngine.EventSystems;

using System.Collections;
using UnityEngine.Networking;
using System.Text;

public class NodeRedPostShiba : MonoBehaviour, IPointerClickHandler
{
    // アクセスする URL
    string urlNodeRed = "ここにサーバーURLを入れる";

    void Start()
    {
        Debug.Log($"Start");
    }

    public void OnPointerClick(PointerEventData eventData)
    {
        // マウスクリックイベント
        Debug.Log($"オブジェクト {this.name} がクリックされたよ！");

        // HTTP GET リクエストを非同期処理を待つためコルーチンとして呼び出す
        StartCoroutine(PostNodeRed());
    }

    // POST リクエストする本体
    IEnumerator PostNodeRed()
    {
        // HTTP リクエストする(POST メソッド) UnityWebRequest を呼び出し
        // アクセスする先は変数 urlNodeRed で設定
        UnityWebRequest request = new UnityWebRequest(urlNodeRed, "POST");

        // 今回は空データを送る
        byte[] bodyRaw = Encoding.UTF8.GetBytes("{}");
        // アップロード（Unity→サーバ）のハンドラを作成
        request.uploadHandler = new UploadHandlerRaw(bodyRaw);
        // ダウンロード（サーバ→Unity）のハンドラを作成
        request.downloadHandler = new DownloadHandlerBuffer();

        // リクエスト開始
        yield return request.SendWebRequest();

        // 結果によって分岐
        switch (request.result)
        {
            case UnityWebRequest.Result.InProgress:
                Debug.Log("リクエスト中");
                break;

            case UnityWebRequest.Result.Success:
                Debug.Log("リクエスト成功");

                // コンソールに表示
                Debug.Log($"responseData: {request.downloadHandler.text}");

                string urlShiba = request.downloadHandler.text;

                // テクスチャ割り当て
                StartCoroutine(GetTexture(urlShiba));


                break;
        }

        request.Dispose();
    }

    IEnumerator GetTexture(string urlTexture)
    {
        // テクスチャを GET リクエストで読み込む。
        UnityWebRequest request = UnityWebRequestTexture.GetTexture(urlTexture);

        // リクエスト開始
        yield return request.SendWebRequest();

        Debug.Log("GetTexture リクエスト開始");

        // 結果によって分岐
        switch (request.result)
        {
            case UnityWebRequest.Result.InProgress:
                Debug.Log("GetTexture リクエスト中");
                break;

            case UnityWebRequest.Result.Success:
                Debug.Log("GetTexture リクエスト成功");

                // テクスチャに割り当て
                Texture loadedTexture = ((DownloadHandlerTexture)request.downloadHandler).texture;

                GameObject.Find("Cube").GetComponent<MeshRenderer>().material.SetTexture("_MainTex", loadedTexture);

                break;
        }
    }
}
```

保存できたら以下のコードに注目します。

```csharp
    // アクセスする URL
    string urlNodeRED = "ここにサーバーURLを入れる";
```

「ここにサーバーURLを入れる」の部分を、今回の URL http://127.0.0.1:1880/api/post/shiba に変更しておきます。

## Cube の大きさを調整

![02-unity-setting_05.png](images/02-unity-setting/02-unity-setting_05.png)

Cube がこのままだと少し小さく確認しにくいので大きさを調整します。

![02-unity-setting_00.png](images/02-unity-setting/02-unity-setting_00.png)

Inspector で Transform を確認します。いま、Scale X:1 Y:1 Z:1 になっています。

![02-unity-setting_04.png](images/02-unity-setting/02-unity-setting_04.png)

Scale X:3 Y:3 Z:3 に変更します。

![02-unity-setting_02.png](images/02-unity-setting/02-unity-setting_02.png)

このような大きさになりました。

これで Unity の準備はできました。

