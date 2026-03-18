## 4.5 Chain─LangChain Expression Language（LCEL）の概要
LLMアプリケーションでは、単にLLMに入力して出力を得て終わりではなく、処理を連鎖的につなぎたいことが多いです。

たとえば、次のような連鎖が考えられます。

- Prompt templateを穴埋めして、その結果をChat modelに与え、その結果をPythonのオブジェクトに変換したい
- 第2章で紹介したZero-shot CoTプロンプティングでステップバイステップで考えさせて、その結果を要約させたい
- LLMの出力を得たあとで、その内容がサービスのポリシーに違反しないか（たとえば差別的な表現でないか）チェックしたい
- LLMの出力結果をもとに、SQLを実行してデータを分析させてみたい

このような処理の連鎖を実現するのが、LangChainの「Chain」です。

### LangChain Expression Language（LCEL）とは
LangChain Expression Language（LCEL）は、LangChainでのChainの記述方法です。LCELではプロンプトやLLMを「|」でつなげて書き、処理の連鎖（Chain）を実装します。

```pyhton
from langchain_core.prompts import ChatPromptTemplate
 from langchain_openai import ChatOpenAI

 prompt = ChatPromptTemplate.from_messages(
     [
         ("system", "ユーザーが入力した料理のレシピを考えてください。"),
         ("human", "{dish}"),
     ]
 )

 model = ChatOpenAI(model_name="gpt-4o-mini", temperature=0)
 chain = prompt | model

 ai_message = chain.invoke({"dish": "カレー"})
 print(ai_message.content)
```

**prompt（PromptTeamplte）の穴埋めと、model（ChatOpenAI）の呼び出しが、連鎖的に実行されたということです。
LCELでは、上記の「chain = prompt | model」のように、プロンプトやLLMを「|」でつなげて書き、処理の連鎖（Chain）を実装します。**
（OutputParserをChainに加える方法もある。）

### Chainのまとめ
LangChainの重要なコンセプトである「Chain」と、自由自在にChainを実装するための「LangChain Expression Language（LCEL）」の概要を解説しました。
LangChainでは、Prompt template・LLM/Chat model・Output parserを連結して、Chainとして一連の処理を実行するのが基本となります。
LCELはより複雑な処理の連鎖を実現することもできます。たとえば、独自の関数をChainにはさんだり、複数のChainを並列につないで実行することも可能です。
このようなLCELのより実践的な使い方については、次の第5章で解説します。

## 4.6 LangChainのRAGに関するコンポーネント
### RAG（Retrieval-Augmented Generation）
入力をもとに文書を検索して、検索結果をcontextに含めてLLMに回答させる手法があります。
このような手法はRAG（Retrieval-Augmented Generation）注9と呼ばれます。
RAGの典型的な構成としては、ベクターデータベースを使い、文書をベクトル化して保存しておいて、入力のテキストとベクトルの近い文書を検索してcontextに含めます。
文書のベクトル化にはOpenAIのEmbeddings APIなどを使用します。

なお、テキストのベクトル化とは、テキストを数値の配列に変換することです。
テキストのベクトル化にはさまざまな手法がありますが、一般に、登場するキーワードや意味が近いテキストがベクトルとしても距離が近くなるように変換します。
テキストのベクトル化自体は最近登場した技術ではなく、自然言語処理の分野で以前からよく使われています。

### LangChainのRAGに関するコンポーネントの概要
LangChainでは、RAGに使用するためのさまざまなコンポーネントが提供されています。まず押さえたい主要なコンポーネントとしては、次の5つがあります。

- Document loader：データソースからドキュメントを読み込む
- Document transformer：ドキュメントに何らかの変換をかける
- Embedding model：ドキュメントをベクトル化する
- Vector store：ベクトル化したドキュメントの保存先
- Retriever：入力のテキストと関連するドキュメントを検索する

これらは情報源（ソース）となるデータからRetrieverによる検索まで、図4.9のようにつながります。
<img width="603" height="227" alt="スクリーンショット 2026-03-18 15 39 54" src="https://github.com/user-attachments/assets/5d86cf04-d299-4a0c-ab54-c690f234fd39" />

### LCELを使ったRAGのChainの実装
チャットボットなどのアプリケーションとしては、入力に関連する文書を検索（Retrieve）するのに加えて、検索結果をPromptTemplateにcontextとして埋め込んで、LLMに質問して回答（QA）してもらいたい場合があります。
<img width="587" height="242" alt="スクリーンショット 2026-03-18 17 37 38" src="https://github.com/user-attachments/assets/adb2f0fa-ec8f-42b9-b2f9-27fd3adfde83" />

### LangChainのRAGに関するコンポーネントのまとめ
この節では、LangChainのRAGに関するコンポーネントの基本を解説してきました。
これらのコンポーネントを使うことで、たとえば社内文書に対してQ&Aが可能なチャットボットを実装することができます。
RAGはLLMアプリケーションで非常によく採用され、さまざまな工夫が考案されています。本書の第6章では、より高度なRAGの手法を扱います。
