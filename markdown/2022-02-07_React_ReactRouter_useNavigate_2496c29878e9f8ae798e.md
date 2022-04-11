<!--
title:   【React】React Router v6でのuseNavigateでのStateの渡し方
tags:    React,ReactRouter,useNavigate
id:      2496c29878e9f8ae798e
private: false
-->

# 初めに

react router をバージョンアップをした際に、useHistory が廃止され useNavigate が追加されました。
useHistory で使っていたが useNavigate での使い方がいまいちわかりずらかったので記事にします。

### やりたいこと

useHistory の state を useNavigate で使う

### 環境

React 17.0.2
React router 6.2.1
typescript 4.2.3

# 本文

フックを利用すると、遷移先に state を渡すことができます。

## v5 の時

useHistory を使って、フラッシュメッセージ機能を実装しています。
ボタンを押すとルートに遷移し、state のメッセージをフラッシュメッセージとして表示する

```tsx:src/RootButton.tsx
import { FC } from 'react'
import { useHistory } from 'react-router-dom'

const RootButton:FC = ()=> {
  const hisotry = useHistory()

  const toRoot = () => {
    hisotry.push('/',{message: '成功',type: 'success'}) //第一引数に遷移するパス、第二変数にstateを渡す
  }

  return (
      <button type="button" onClick={toRoot}>
        Root
      </button>
    )
}

```

```tsx:src/FlashMessage.tsx
import { FC,useState } from 'react';
import { Message } from 'semantic-ui-react';
import { useLocation } from 'react-router-dom'


const FlashMessage:FC = ()=>{
  const location = useLocation(); //location.stateでhistory.pushの引数で渡したstateを取り出すことができる
  const [state,setState] = useState(false);
  const dismiss = ()=> setState((prevState)=> prevState && false)

  return (
    <>
      {state &&
        (location.state.type === 'success' ? (
          <Message success onDismiss={dismiss}>
            <Message.Header
              content={location.state.message}
            />
          </Message>
        ) : (
          <Message error onDismiss={dismiss}>
            <Message.Header
              content={message}
            />
          </Message>
        ))}
    </>
  );
}

```

これでボタンを押したら、ルートに遷移しフラッシュメッセージが表示されます。

## v6 の時

バージョンが 6 になると、useHistory を useNavigate に書き換える必要があります。
history.push を navigate に書き換えます。

```tsx:src/RootButton.tsx
import { FC } from 'react'
import { useNavigate } from 'react-router-dom'

const RootButton:FC = ()=> {
  const navigate = useNavigate()

  const toRoot = () => {
    navigate('/',{state: {message: '成功',type: 'success' }})
　　　　　　　　//第一引数に遷移するパス、第二変数にstateをオブジェクトで渡す
  }

  return (
      <button type="button" onClick={toRoot}>
        Root
      </button>
    )
}


```

```tsx:src/FlashMessage.tsx
import { FC,useState } from 'react';
import { Message } from 'semantic-ui-react';
import { useLocation } from 'react-router-dom';


const FlashMessage:FC = ()=>{
  const location = useLocation(); //location.stateでstateを取り出すことができる 型が変わっているので注意
  const [state,setState] = useState(false);
  const dismiss = ()=> setState((prevState)=> prevState && false);
  type MessageState = {message: string, type: 'success' | 'error';
  const messagestate = location.state as MessageState; //型を無理やり与える

  return (
    <>
      {state &&
        (messagestate.type === 'success' ? (
          <Message success onDismiss={dismiss}>
            <Message.Header
              content={messagestate.message}
            />
          </Message>
        ) : (
          <Message error onDismiss={dismiss}>
            <Message.Header
              content={messagestate.message}
            />
          </Message>
        ))}
    </>
  );
}

```

バージョンが上がって state がオプションの一部に変更されたので、第二引数のオブジェクトの中で渡す必要があります。

# まとめ

v6 にバージョンを上げるのは簡単ですが、Switch を書き換えたりと面倒なことが多いですが Router が分割して書くことができるようになったのは便利になりました。

### 参考

・[公式アップグレードガイド](https://reactrouter.com/docs/en/v6/upgrading/v5)
