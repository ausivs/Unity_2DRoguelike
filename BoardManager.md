```c#
using System;
using System.Collections;
using System.Collections.Generic;
using Unity.VisualScripting;
using UnityEngine;
using UnityEngine.Tilemaps;
using Random = UnityEngine.Random;

/// <summary>
/// マップ生成管理
/// </summary>
public class BoardManager : MonoBehaviour {

	/// <summary>
	/// アイテム生成管理
	/// </summary>
	[Serializable]
	public class ItemCount {
		/// <summary>
		/// 生成するアイテムの最小個数
		/// </summary>
		public int minimum;
		/// <summary>
		/// 生成するアイテムの最大個数
		/// </summary>
		public int maximum;

		/// <summary>
		/// コンストラクタ. 生成するアイテムの最小個数と最大個数を設定
		/// </summary>
		/// <param name="min"></param>
		/// <param name="max"></param>
		public ItemCount(int min, int max) {
			minimum = min;
			maximum = max;
		}
	}

	/// <summary>
	/// Mapの幅Width
	/// </summary>
	public const int columns = 8;
	/// <summary>
	/// Mapの高さHeight
	/// </summary>
	public const int rows = 8;


	/// <summary>
	/// 破壊できる壁の生成数
	/// </summary>
	public ItemCount wallCount = new(3, 9);
	/// <summary>
	/// 食料の生成数
	/// </summary>
	public ItemCount foodCount = new(1, 5);

	// Mapの材料
	/// <summary>
	/// ボード インスタンス
	/// </summary>
	public GameObject boardHolderObj;
	/// <summary>
	/// 出口 インスタンス(プレハブを格納する)
	/// </summary>
	public GameObject exit;
	/// <summary>
	/// 床 インスタンス(プレハブを格納する)
	/// </summary>
	public GameObject floor;
	/// <summary>
	/// 外壁 インスタンス(プレハブを格納する)
	/// </summary>
	public GameObject outerWall;
	/// <summary>
	/// 敵 インスタンス(プレハブを格納する)
	/// </summary>
	public GameObject enemy;
	/// <summary>
	/// 破壊できる壁 インスタンス(プレハブを格納する)
	/// </summary>
	public GameObject wall;
	/// <summary>
	/// 食料 インスタンス(プレハブを格納する)
	/// </summary>
	public GameObject food;

	/// <summary>
	/// ボード トランスフォーム(BoardゲームオブジェクトのTransform)
	/// </summary>
	Transform boardHolder;

	/// <summary>
	/// アイテム類を配置するグリッド リスト
	/// </summary>
	List<Vector3> gridPositions = new List<Vector3>();

	void Awake() {
		BoardSetup();

		InitList();

		LayoutObjectAtRandom(wall, wallCount.minimum, wallCount.maximum);
		LayoutObjectAtRandom(food, foodCount.minimum, foodCount.maximum);

		Instantiate(exit, new Vector3(columns - 1, rows - 1, 0), Quaternion.identity);
	}
	
	/// <summary>
	/// 初期化Init
	/// </summary>
	void BoardSetup() {

		//	ボードのtransformを取得
		boardHolder = boardHolderObj.transform;

		for (int y = -1; y < rows + 1; y++) {
			for (int x = -1; x < columns + 1; x++) {

				if (x == -1 || x == columns || y == -1 || y == rows) {

					Instantiate(outerWall, new Vector3(x, y, 0), Quaternion.identity, boardHolder);
				}
				else {
					//	インスタンスを生成する
					Instantiate(floor, new Vector3(x, y, 0), Quaternion.identity, boardHolder);
				}
			}
		}
	}

	void InitList() {
		//
		gridPositions.Clear();

		//	6x6(外周は何も操作しない)
		for (int y = 1; y < rows - 1; y++) {
			for (int x = 1; x < columns - 1; x++) {

				//
				gridPositions.Add(new Vector3(x, y, 0));
			}
		}
	}
	
	/// <summary>
	/// gridPositions からランダムな位置を取得する
	/// </summary>
	/// <returns></returns>
	Vector3 RandomPosition() {

		Vector3 retPos = new();

		int randomIdx = Random.Range(0, gridPositions.Count);

		retPos = gridPositions[randomIdx];



		gridPositions.RemoveAt(randomIdx);



		return retPos;
	}

	/// <summary>
	/// マップにアイテムを配置する
	/// </summary>
	/// <param name="tile"> 配置するアイテムのタイル </param>
	/// <param name="min"></param>
	/// <param name="max"></param>
	void LayoutObjectAtRandom(GameObject tile, int min, int max) {

		int objCount = Random.Range(min, max);

		for (int i = 0; i < objCount; i++) {

			Vector3 randomPos = RandomPosition();

			Instantiate(tile, randomPos, Quaternion.identity);
		}
	}
}
```
