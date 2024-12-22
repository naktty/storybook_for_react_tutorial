# データの配線
UIコンポーネントにデータを配線する方法を学ぶ

これまでのところ、私たちは孤立したステートレス・コンポーネントを作成してきた。

このチュートリアルでは、アプリの構築に関する詳細には触れません。しかし、コネクテッド・コンポーネントにデータを配線するための一般的なパターンを少し見てみましょう。

## 連結コンポーネント
現在書かれているTaskListコンポーネントは、それ自身の実装の外部とは何も話さないという意味で、「プレゼンテーション的」なものです。このコンポーネントにデータを取り込むには、データ・プロバイダに接続する必要がある。

この例では、Reduxでデータを保存するアプリケーションを開発するための最も効果的なツールセットであるRedux Toolkitを使って、アプリ用のシンプルなデータモデルを構築します。しかし、ここで使われているパターンは、ApolloやMobXのような他のデータ管理ライブラリにも同様に適用できます。

必要な依存関係をプロジェクトに追加します

```bash
yarn add @reduxjs/toolkit react-redux
```

まず、タスクの状態を変更するアクションに反応するシンプルなReduxストアを、src/libディレクトリのstore.jsというファイルに作成する（意図的にシンプルにしてある）

```javascript
/* src/lib/store.js */

/* A simple redux store/actions/reducer implementation.
 * A true app would be more complex and separated into different files.
 */
import { configureStore, createSlice } from '@reduxjs/toolkit';

/*
 * The initial state of our store when the app loads.
 * Usually, you would fetch this from a server. Let's not worry about that now
 */
const defaultTasks = [
  { id: '1', title: 'Something', state: 'TASK_INBOX' },
  { id: '2', title: 'Something more', state: 'TASK_INBOX' },
  { id: '3', title: 'Something else', state: 'TASK_INBOX' },
  { id: '4', title: 'Something again', state: 'TASK_INBOX' },
];
const TaskBoxData = {
  tasks: defaultTasks,
  status: 'idle',
  error: null,
};

/*
 * The store is created here.
 * You can read more about Redux Toolkit's slices in the docs:
 * https://redux-toolkit.js.org/api/createSlice
 */
const TasksSlice = createSlice({
  name: 'taskbox',
  initialState: TaskBoxData,
  reducers: {
    updateTaskState: (state, action) => {
      const { id, newTaskState } = action.payload;
      const task = state.tasks.findIndex((task) => task.id === id);
      if (task >= 0) {
        state.tasks[task].state = newTaskState;
      }
    },
  },
});

// The actions contained in the slice are exported for usage in our components
export const { updateTaskState } = TasksSlice.actions;

/*
 * Our app's store configuration goes here.
 * Read more about Redux's configureStore in the docs:
 * https://redux-toolkit.js.org/api/configureStore
 */
const store = configureStore({
  reducer: {
    taskbox: TasksSlice.reducer,
  },
});

export default store;
```

次に、TaskListコンポーネントを更新して、Reduxストアに接続し、興味のあるタスクをレンダリングする。

```jsx
import Task from './Task';

import { useDispatch, useSelector } from 'react-redux';

import { updateTaskState } from '../lib/store';

export default function TaskList() {
  // We're retrieving our state from the store
  const tasks = useSelector((state) => {
    const tasksInOrder = [
      ...state.taskbox.tasks.filter((t) => t.state === 'TASK_PINNED'),
      ...state.taskbox.tasks.filter((t) => t.state !== 'TASK_PINNED'),
    ];
    const filteredTasks = tasksInOrder.filter(
      (t) => t.state === 'TASK_INBOX' || t.state === 'TASK_PINNED'
    );
    return filteredTasks;
  });

  const { status } = useSelector((state) => state.taskbox);

  const dispatch = useDispatch();

  const pinTask = (value) => {
    // We're dispatching the Pinned event back to our store
    dispatch(updateTaskState({ id: value, newTaskState: 'TASK_PINNED' }));
  };
  const archiveTask = (value) => {
    // We're dispatching the Archive event back to our store
    dispatch(updateTaskState({ id: value, newTaskState: 'TASK_ARCHIVED' }));
  };
  const LoadingRow = (
    <div className="loading-item">
      <span className="glow-checkbox" />
      <span className="glow-text">
        <span>Loading</span> <span>cool</span> <span>state</span>
      </span>
    </div>
  );
  if (status === 'loading') {
    return (
      <div className="list-items" data-testid="loading" key={"loading"}>
        {LoadingRow}
        {LoadingRow}
        {LoadingRow}
        {LoadingRow}
        {LoadingRow}
        {LoadingRow}
      </div>
    );
  }
  if (tasks.length === 0) {
    return (
      <div className="list-items" key={"empty"} data-testid="empty">
        <div className="wrapper-message">
          <span className="icon-check" />
          <p className="title-message">You have no tasks</p>
          <p className="subtitle-message">Sit back and relax</p>
        </div>
      </div>
    );
  }

  return (
    <div className="list-items" data-testid="success" key={"success"}>
      {tasks.map((task) => (
        <Task
          key={task.id}
          task={task}
          onPinTask={(task) => pinTask(task)}
          onArchiveTask={(task) => archiveTask(task)}
        />
      ))}
    </div>
  );
}
```

これで、Reduxストアから取得した実際のデータがコンポーネントに入力されるようになったので、それをsrc/App.jsに配線して、そこでコンポーネントをレンダリングすることもできた。しかし、今はそれを控えて、コンポーネント駆動の旅を続けよう。

心配はいらない。次の章で説明する。
