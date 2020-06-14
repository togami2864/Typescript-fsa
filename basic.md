# Typescript-fsaの基礎

## actionsの作成    
actionCreatorを作成する
```
import actionCreatorFactory from "typescript-fsa";  

const actionCreator = actionCreatorFactory();
``` 
↑冒頭必ず必要な呪文  

```
const profileActions = {
  setProfile: actionCreator<Partial<Profile>>("SET_PROFILE"),
};
```
actionCreatorのジェネリクス<>：　SET_PROFILEというactionのpayload(reducer)に渡す情報の型を定義 

```<Partial<Profile>> ```   
Profileの部分集合。必要なものだけ渡す。(他はundefined)一つずつ更新したい時に。  
**もともと定義されていないものに対してはerror!**



## reducerの作成
```
import { reducerWithInitialState } from "typescript-fsa-reducers";
import { Profile } from "../../domain/entity/profile";
import profileActions from "./actions";

const init: Profile = {
  name: "",
  description: "",
  birthday: "",
  gender: "",
};

const profileReducer = reducerWithInitialState(init).case(
  profileActions.setProfile,
  (state, payload) => ({
    ...state,
    ...payload,
  })
);

export default profileReducer;
```
・reduxにおいてはreducerで初期値を決定  
・**redux では直接値を更新してはいけないのでスプレット構文で一度展開し、更新文を反映**
```
import { reducerWithInitialState } from "typescript-fsa-reducers";

const profileReducer = reducerWithInitialState(init).case()
```
↑呪文  
### .case  
チェーンしてそれぞれのアクションでの処理(従来のswitchの役目)  
**第一引数**: typeの種類  
**第二引数**: callback  

## storeの作成
```
import { createStore, combineReducers } from "redux";
//呪文↑
import profileReducer from "./profile/reducer";
import { RootState } from "../domain/entity/rootState";

const store = createStore(
  combineReducers<RootState>({
    profile: profileReducer,
  }),
  //devtoolに接続
  (window as any).__REDUX_DEVTOOLS_EXTENSION__ &&
    (window as any).__REDUX_DEVTOOLS_EXTENSION__()
   //
);

export default store;
```  

##大まかな流れ  
データ構造の定義 => actionの作成 => reducer => store

