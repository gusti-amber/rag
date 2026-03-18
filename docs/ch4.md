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
