## GitHub の中身

![02-unity-setting_16.png](image/02-unity-setting_16.png)

Hierarchy を確認します。

## EventSystem の配置

![02-unity-setting_14.png](image/02-unity-setting_14.png)

Hierarchy で上部の＋ボタンから UI > EventSystem を選択します。

![02-unity-setting_23.png](image/02-unity-setting_23.png)

EventSystem が配置されます。

## MainCamera に PhysicsRaycaster コンポーネントの配置

![02-unity-setting_10.png](image/02-unity-setting_10.png)

MainCamera をクリックします。

![02-unity-setting_17.png](image/02-unity-setting_17.png)

MainCamera を選択した状態で Inspector を確認して Add Component をクリックします。

![02-unity-setting_03.png](image/02-unity-setting_03.png)

コンポーネントの選択画面が出てきます。

![02-unity-setting_12.png](image/02-unity-setting_12.png)

検索エリアに PhysicsRaycaster を検索してクリックして選択します。

![02-unity-setting_13.png](image/02-unity-setting_13.png)

PhysicsRaycaster が追加されました。

## Cube の配置

![02-unity-setting_07.png](image/02-unity-setting_07.png)

Hierarchy で上部の＋ボタンから 3D Object > Cude の操作をして Cube オブジェクトを配置します。

![02-unity-setting_09.png](image/02-unity-setting_09.png)

Cube というオブジェクトが作成されます。

![02-unity-setting_21.png](image/02-unity-setting_21.png)

Cube を選択した状態で Inspector を確認して Add Component をクリックします。

![02-unity-setting_11.png](image/02-unity-setting_11.png)

New script をクリックします。

![02-unity-setting_24.png](image/02-unity-setting_24.png)

Name が聞かれるので NodeRedGetJson と入力して Create and Add をクリックします。

![02-unity-setting_01.png](image/02-unity-setting_01.png)

コンポーネントが追加されたら、C# スクリプトを編集するために、こちらをダブルクリックします。

![02-unity-setting_19.png](image/02-unity-setting_19.png)

エディタが起動します。NodeRedGetRequest のスクリプトは以下を記述して保存します。

<div>[csharp]
using UnityEngine;
using UnityEngine.EventSystems;

using System.Collections;
using UnityEngine.Networking;
using System;
using TMPro;

public class NodeRedGetJson : MonoBehaviour, IPointerClickHandler
{
    // アクセスする URL
    string urlNodeRed = "ここにサーバーURLを入れる";

    // JSON データ化する ResponseData ベースクラス
    [Serializable]
    public class ResponseData
    {
        // value1 というプロパティ名で string 型で変換
        public string value1;
        // value2 というプロパティ名で int 型で変換
        public int value2;
        // value3 というプロパティ名で bool 型で変換
        public bool value3;
    }

    void Start()
    {
        Debug.Log($"Start");
    }

    public void OnPointerClick(PointerEventData eventData)
    {
        // マウスクリックイベント
        Debug.Log($"オブジェクト {this.name} がクリックされたよ！");

        // HTTP GET リクエストを非同期処理を待つためコルーチンとして呼び出す
        StartCoroutine(GetNodeRed());
    }

    // GET リクエストする本体
    IEnumerator GetNodeRed()
    {
        // HTTP リクエストする(GET メソッド) UnityWebRequest を呼び出し
        // アクセスする先は変数 urlNodeRed で設定
        UnityWebRequest request = UnityWebRequest.Get(urlNodeRed);

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

                // そのうえで ResponseData クラスで Unity で扱えるデータ化
                ResponseData responseData = JsonUtility.FromJson<ResponseData>(request.downloadHandler.text);

                // テキストに反映
                GameObject.Find("Text1").GetComponent<TextMeshPro>().text = responseData.value1;


                break;
        }

        request.Dispose();


    }
}
[/csharp]</div>

保存できたら以下のコードに注目します。

<div>[csharp]
    // アクセスする URL
    string urlNodeRED = "ここにサーバーURLを入れる";
[/csharp]</div>

「ここにサーバーURLを入れる」の部分を、今回の URL http://127.0.0.1:1880/api/get/json に変更しておきます。

## TextMeshPro の配置・調整

![02-unity-setting_20.png](image/02-unity-setting_20.png)

Hierarchy で上部の＋ボタンから 3D Object > Text - TextMeshPro を選択します。

![02-unity-setting_15.png](image/02-unity-setting_15.png)

TMP Importer が表示されるので Import TMP Essentials をクリックします。

![02-unity-setting_04.png](image/02-unity-setting_04.png)

読み込まれたら閉じます。

![02-unity-setting_18.png](image/02-unity-setting_18.png)

Text (TMP) という名前で TextMeshPro が配置されました。

![02-unity-setting_08.png](image/02-unity-setting_08.png)

Text (TMP) をクリックすると名前が編集できるので Text1 と入力します。

![02-unity-setting_22.png](image/02-unity-setting_22.png)

名前が変更できました。

![02-unity-setting_00.png](image/02-unity-setting_00.png)

このままだと、テキストが大きすぎるのでちょっと調整します。

![02-unity-setting_06.png](image/02-unity-setting_06.png)

引き続き Text1 を選択した状態で Inspector を確認し Rect Transform に注目します。

![02-unity-setting_02.png](image/02-unity-setting_02.png)

- Pos X : 0
- Pos Y : 0
- Pos Z : 0
- Scale X : 0.5
- Scale Y : 0.5
- Scale Z : 0.5

と入力します。

![02-unity-setting_05.png](image/02-unity-setting_05.png)

テキストがこのように配置されます。

これで Unity の準備はできました。

