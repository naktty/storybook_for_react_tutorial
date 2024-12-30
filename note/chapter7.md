# ビジュアル・テスト
UIコンポーネントのテスト方法を学ぶ
Storybookのチュートリアルはテストなしでは完結しません。テストは高品質のUIを作成するために不可欠です。モジュラーシステムでは、わずかな調整が大きなリグレッションにつながる可能性があります。これまで、私たちは3種類のテストに遭遇してきました

* 手動テストは、コンポーネントの正しさを検証するために、開発者が手動でコンポーネントを見ることに依存する。手動テストは、開発者がコンポーネントを手動で見て、正しさを確認するのに役立ちます。
* a11yアドオンによるアクセシビリティテストは、コンポーネントが誰にでもアクセス可能であることを検証します。これは、ある種の障害を持つ人々がどのようにコンポーネントを使用するかについての情報を収集するのに適しています。
* play機能によるコンポーネントテストは、コンポーネントとインタラクトしたときにコンポーネントが期待通りに動作するかを検証します。コンポーネントが使用されているときの動作をテストするのに最適です。

## 「でも、ちゃんと見える？」
残念ながら、前述のテスト方法だけではUIのバグを防ぐには不十分だ。デザインは主観的でニュアンスが異なるため、UIのテストは厄介だ。手動テストは、まあ手動だ。スナップショットテストのような他のUIテストは誤検出が多すぎるし、ピクセルレベルのユニットテストは評価が低い。完全なストーリーブックのテスト戦略には、視覚的なリグレッションテストも含まれる。

## ストーリーブックのビジュアルテスト
ビジュアルリグレッションテストはビジュアルテストとも呼ばれ、見た目の変更をキャッチするように設計されています。すべてのストーリーのスクリーンショットをキャプチャし、コミットごとに比較することで、変更を表面化します。レイアウト、色、サイズ、コントラストのようなグラフィカルな要素の検証に最適です。

ストーリーブックは、ビジュアルなリグレッションテストのための素晴らしいツールです。ストーリーを書いたり更新したりするたびに、無料で仕様書を手に入れることができる！

ビジュアル回帰テスト用のツールはいくつかあります。私たちは、Storybookのメンテナによって作られた、ビジュアルテストを高速なクラウドブラウザ環境で実行する無料のパブリッシングサービス、Chromaticを推奨する。また、前の章で見たように、Storybookをオンラインで公開することもできます。