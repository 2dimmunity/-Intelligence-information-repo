# -Intelligence-information-repo

#ファイル
LiDAR点群を用いて、画像から生成した擬似点群を補正するgraph depth correction(GDC)に関連するファイル群。
mian_batch.py … GDCを全て（指定）のデータに対して、gdc.pyのGDCを実行して、補正した結果の深度マップを保存するファイル。
gdc.py … GDCを実行するファイル。
depthmap2ptc_with_mask.py …　GDCで補正した深度マップを点群データに変換するためのファイル。点群データに変換する際に、深度マップのデータと、識別情報のデータを統合するようにしている。
kitti_util.py …　カメラとLiDAR間のキャリブレーションを行うためのutil。今回はキャリブレーションを行う際に、識別情報なども変換後に反映される必要があるため、一部関数を追加。

#目的
LiDAR点群データは, (x, y, z, 反射強度)の4チャンネルから成るデータ。
画像とLiDARの補正によって生成する擬似点群に, 画像由来の点か、LiDAR由来の点かを識別する情報を付加して、(x, y, z, 反射強度, 識別情報)の5チャンネルから成るデータを生成することが目的。
※識別情報をLiDARなら1, 画像なら0　という風に与えることにした。そのバイナリ情報をファイルの中では,~with_mask のように表現していますが、maskの意味とはズレていることにあとで気づいたのですが、ご容赦ください。


#詳細
ベースの実装はPseudo-LiDAR++に基づいている(https://github.com/mileyan/Pseudo_Lidar_V2)

・main_batch.py 
  GDC_and_mask_saveでマスク情報を深度マップと別で保存するように変更。
  
・gdc.py
  GDCの結果として、補正結果の深度マップだけでなく、LiDAR点群で深度を置き換えたピクセルの情報も返すように変更。
  
・depthmap2ptc_with_mask.py 
  GDCで補正した深度マップを点群データに変換するためのファイル。点群データに変換する際に、深度マップのデータと、識別情報のデータを統合するようにしている。
  convert_and_save_with_maskでは、
  深度マップを3D点群に変換して、識別情報(mask)を加える "depth2ptc_with_mask"
  それをカメラ視点の軸から、LiDAR視点の軸へと変換する "project_rect_to_velo_with_mask"
  などを通じて識別情報を持った3次元点群データへ変換される。これらの関数の実装は, kitti_util.pyの中。
  
 ・kitti_util.py
    project_rect_to_velo_with_maskや、project_image_to_rect_with_maskなどを追加して、キャリブレーションの前後で識別情報が保持されるように調整した。
  
    
 #その他細かい調整
 レポート中の(A)64beamでの補正や、(B)可視化などについては、KITTIデータにおける基本のファンクション（https://github.com/kuangliu/kitti-utils）, 既存のライブラリ(https://github.com/kuixu/kitti_object_vis) の組み合わせや細かい調整なので割愛する。
 （c）の識別付加情報付加後の学習と推論については、num_input_featuresを4から5に変更して、一部調整するだけなのでこれも割愛する。
