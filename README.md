# MapLibre GL JSで秋田県津波浸水想定マップ（最大浸水深及び浸水開始時間）を表示するデモサイト
## デモサイト
> **重要:**
> デモサイトでは、秋田県のWebサイトにてオープンデータとして公開されている、[津波浸水想定データ（最大浸水深及び浸水開始時間）](https://www.pref.akita.lg.jp/pages/archive/53932)を表示しています。

### 国土地理院 最適化ベクトルタイルと最大浸水深及び浸水開始時間を合成して重ね合わせ
> **最大浸水深及び浸水開始時間の不透明度：100%**  
https://shi-works.github.io/akita-earthquake-tsunami-damage-map-on-maplibre-gl-js/
<figure>
  <img src="https://github.com/shi-works/akita-earthquake-tsunami-damage-map-on-maplibre-gl-js/assets/71203808/736746f3-ab62-46d5-ab15-dba73946dd2b" alt="代替テキスト">
  <figcaption>最大浸水深</figcaption>
</figure>

<figure>
  <img src="https://github.com/shi-works/akita-earthquake-tsunami-damage-map-on-maplibre-gl-js/assets/71203808/50d85dd5-9456-4474-acb6-2f640ce0f77d" alt="代替テキスト">
  <figcaption>浸水開始時間</figcaption>
</figure>

## 最大浸水深及び浸水開始時間（PMTiles形式）
- 概要：秋田県のWebサイトにてオープンデータとして公開されている、[津波浸水想定データ（最大浸水深及び浸水開始時間、シェープファイル）](https://www.pref.akita.lg.jp/pages/archive/53932)を[PMTiles](https://github.com/protomaps/PMTiles)形式に変換したデータです。
- 最大浸水深及び浸水開始時間のPMTiles形式のデータは[CC BY 4.0](https://creativecommons.org/licenses/by/4.0/deed.ja)で公開しています。
- 津波浸水想定の詳細については、[津波浸水想定について（解説）](https://www.pref.akita.lg.jp/pages/archive/53908)を参照してください。
- [最大浸水深データ（PMTiles）](https://xs489works.xsrv.jp/pmtiles-data/pref-akita/maximum_depth.pmtiles)
- [浸水開始時間データ（PMTiles）](https://xs489works.xsrv.jp/pmtiles-data/pref-akita/flooding_start_time.pmtiles)
- 原初データ出典：[津波浸水想定データ（最大浸水深及び浸水開始時間、シェープファイル）](https://www.pref.akita.lg.jp/pages/archive/53932)
  - ライセンス：[秋田県オープンデータ利用規約（CC BYに従うことでも利用可能）](https://www.pref.akita.lg.jp/pages/archive/36756)

## PMTiles形式のデータ作成方法
- 作成時に対象としたデータは下記の[27パターン](https://www.pref.akita.lg.jp/pages/archive/53937)です。
1. 能代断層帯
2. 花輪東断層帯
3. 男鹿地震
4. 天長地震
5. 秋田仙北地震震源北方
6. 北由利断層
7. 秋田仙北地震
8. 横手盆地東縁断層帯北部
9. 横手盆地東縁断層帯南部
10. 真昼山地東縁断層帯北部
11. 真昼山地東縁断層帯南部
12. 象潟地震
13. 横手盆地_真昼山地連動
14. 秋田仙北地震震源北方_秋田仙北地震連動
15. 天長地震_北由利断層連動
16. 津軽山地西縁断層帯南部
17. 折爪断層
18. 雫石盆地西縁断層帯
19. 北上低地西縁断層帯
20. 庄内平野東縁断層帯
21. 新庄盆地断層帯
22. 海域A
23. 海域B
24. 海域C
25. 海域A＋B
26. 海域B＋C
27. 海域A＋B＋C

- 上記の27パターンのシェープファイルをPython（[GDAL/OGR](https://live.osgeo.org/ja/overview/gdal_overview.html)）でFlatGeobuf形式のデータに変換し、リネーム後、下記の[tippecanoe](https://github.com/felt/tippecanoe)のコマンドを実行して作成しています。
- 27パターンのFlatGeobuf形式のデータ（リネーム前）は[こちらからダウンロード（7zip形式）](https://xs489works.xsrv.jp/pmtiles-data/pref-akita/fgb.7z)できます。
- tippecanoeのバージョンはv2.23.0です。
- tippecanoeのオプションは以下のとおりです。

**-P：並列読み込み**
- ただし、下記の記事によると、`-P`オプションが有効なのは、GeoJSONSeqのみのようです。  
[ベクトルタイルのつくりかた（2022年版）](https://qiita.com/Kanahiro/items/ceeb20c158b4c70b62b6)

**-pf：地物数制限を無視する**（1タイルあたり200,000フィーチャに制限しない）

**-pk：ファイルサイズ制限を無視する**（1タイルあたり500Kバイトに制限しない）

**-z, -Z：ズームレベル指定**
- ズームレベルを指定する場合は下記のとおりになります。
- **-Z10 -z18**とすると、ズームレベル10-18の範囲でタイルを生成します。
- 大文字の**Z**が最小ズームレベル、小文字の**z**が最大ズームレベルを示すことに注意が必要です。
- 指定しない場合は自動的に設定されます。
- [こちら](https://github.com/felt/tippecanoe#zoom-levels)でズームレベルごとの地理分解能の概数がわかります。
- 例えば、ズームレベル14で0.5m相当となり、これは一般的なWeb地図では十分な分解能と言えます。

```sh:process_fgb_files.sh
#!/bin/bash

for input_file in *.fgb; do
    output_file="${input_file%.fgb}.pmtiles"
    echo "Processing ${input_file} ..."
    tippecanoe -o "${output_file}" "${input_file}" -Z8 -z10 -pf -pk -P 
done

echo "All files processed."
read -p "Press any key to continue . . . " -n1 -s
```

### PMTilesの閲覧方法
- PMTilesは、[PMTiles Viewer](https://protomaps.github.io/PMTiles/)で閲覧することができます
- 下記はPMTiles Viewerでパターン1を表示した例です。
- url=部分のURLを別のパターンのURLに書き換えると別のパターンでも表示ができます。
- https://protomaps.github.io/PMTiles/?url=https://xs489works.xsrv.jp/pmtiles-data/pref-akita/01.pmtiles#map=8.09/39.765/140.561

## 背景地図及び地形データ
- 国土地理院 最適化ベクトルタイル（PMTiles形式）
    - 出典：https://github.com/gsi-cyberjapan/optimal_bvmap
    - ライセンス：[国土地理院コンテンツ利用規約](https://www.gsi.go.jp/kikakuchousei/kikakuchousei40182.html)に従い、出典明示により、転載も含め使用可
- 国土地理院 地理院タイル（陰影起伏図）
    - 出典：https://maps.gsi.go.jp/development/ichiran.html#hillshademap
    - ライセンス：[国土地理院コンテンツ利用規約](https://www.gsi.go.jp/kikakuchousei/kikakuchousei40182.html)に従い、出典明示により、転載も含め使用可
- 産業技術総合研究所 シームレス標高タイル（統合DEM）
    - 出典：https://tiles.gsj.jp/tiles/elev/tiles.html
    - ライセンス：[産総研地質調査総合センターウェブサイト利用規約](https://www.gsj.jp/license/license.html)に従い、商用を含む自由な二次利用が可能です。この規約はCC BY 4.0と互換です。
