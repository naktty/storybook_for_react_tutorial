# アドオン
人気のControlsアドオンの統合と使用方法を学ぶ

STORYBOOKには、チーム全員の開発者エクスペリエンスを向上させるために使用できるアドオンの堅牢なエコシステムがあります。すべて[こちら](https://storybook.js.org/addons)でご覧ください。

このチュートリアルに沿っていれば、すでに複数のアドオンに出会い、テストの章で1つをセットアップしています。

ありとあらゆるユースケースに対応するアドオンがあり、それらすべてについて書くとなると、永遠に時間がかかりそうです。最も人気のあるアドオンの1つを統合してみましょう： Controlsです。

## Controlsとは？
Controlsを使用すると、デザイナーや開発者は、引数を操作してコンポーネントの動作を簡単に調べることができます。コードは必要ありません。Controlsはストーリーの隣にアドオンパネルを作成し、引数をライブで編集できます。

Storybookを新規にインストールすると、Controlsが最初から含まれています。追加の設定は必要ありません。

## アドオンが新しいStorybookワークフローをアンロック
Storybookは素晴らしいコンポーネント駆動型の開発環境です。ControlsアドオンはStorybookをインタラクティブなドキュメントツールに進化させます。

### コントロールを使ってエッジケースを見つける
コントロールを使って、QAエンジニア、UIエンジニア、その他の関係者は、コンポーネントを限界までプッシュすることができます！次の例で、もし巨大な文字列を追加したら、タスクはどうなるでしょうか？

![alt text](../images/image17.png)

それは正しくない！テキストがTaskコンポーネントの境界を越えてオーバーフローしているように見えます。

コントロールによって、コンポーネントへのさまざまな入力（この場合は長い文字列）をすばやく確認できるようになり、UIの問題を発見するのに必要な作業が減りました。

それでは、Task.jsxにスタイルを追加して、はみ出しの問題を修正しましょう

```jsx
// src/components/Task.jsx

export default function Task({ task: { id, title, state }, onArchiveTask, onPinTask }) {
  return (
    <div className={`list-item ${state}`}>
      <label
        htmlFor={`archiveTask-${id}`}
        aria-label={`archiveTask-${id}`}
        className="checkbox"
      >
        <input
          type="checkbox"
          disabled={true}
          name="checked"
          id={`archiveTask-${id}`}
          checked={state === "TASK_ARCHIVED"}
        />
        <span
          className="checkbox-custom"
          onClick={() => onArchiveTask(id)}
        />
      </label>

      <label htmlFor={`title-${id}`} aria-label={title} className="title">
        <input
          type="text"
          value={title}
          readOnly={true}
          name="title"
          id={`title-${id}`}
          placeholder="Input title"
          style={{ textOverflow: 'ellipsis' }}
        />
      </label>

      {state !== "TASK_ARCHIVED" && (
        <button
          className="pin-button"
          onClick={() => onPinTask(id)}
          id={`pinTask-${id}`}
          aria-label={`pinTask-${id}`}
          key={`pinTask-${id}`}
        >
          <span className={`icon-star`} />
        </button>
      )}
    </div>
  );
}
```

問題は解決しました！ハンサムな省略記号を使用して、テキストがタスク領域の境界に達すると切り捨てられるようになりました。

### リグレッションを回避するための新しいストーリーの追加
将来的には、コントロールズを使って同じ文字列を入力することで、この問題を手動で再現することができる。しかし、このエッジケースを紹介するストーリーを書く方が簡単だ。そうすることで、リグレッションテストのカバレッジが広がり、他のチームのためにコンポーネントの限界を明確に示すことができます。

Task.stories.jsxに、長いテキストの場合の新しいストーリーを追加します

``` jsx
// src/components/Task.stories.jsx

const longTitleString = `This task's name is absurdly large. In fact, I think if I keep going I might end up with content overflow. What will happen? The star that represents a pinned task could have text overlapping. The text could cut-off abruptly when it reaches the star. I hope not!`;

export const LongTitle = {
  args: {
    task: {
      ...Default.args.task,
      title: longTitleString,
    },
  },
};
```

これで、このエッジケースを簡単に再現して作業できるようになった。

ビジュアル・テストであれば、切り捨ての解決策が破られた場合も通知される。極端なエッジケースは、テストカバレッジがないと忘れられがちです！

💡 Controlsは、開発者以外の人にコンポーネントやストーリーを遊んでもらうのに最適です。ここで見た以上のことができます。公式ドキュメントを読んで、もっと詳しく知ることをお勧めします。しかし、アドオンを使ってワークフローに合わせてStorybookをカスタマイズできる方法は他にもたくさんあります。アドオンの作成ガイドでは、開発ワークフローを大幅に改善するアドオンを作成する方法を紹介します。

### 変更をマージする
変更をgitにマージすることを忘れないでください！
