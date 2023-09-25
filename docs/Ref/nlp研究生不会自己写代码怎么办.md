<https://www.zhihu.com/question/615441114/answer/3150040683>

挑一个最简单的框架

（我觉得[llama 2](https://www.zhihu.com/search?q=llama%202&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A3150040683%7D)就不错，generate才300行）

然后在上面魔改成一个只有你自己看得懂的屎山

发论文够了

* * *

23.8.28更新：

我随手写的东西能有好几十个[收藏](https://www.zhihu.com/search?q=%E6%94%B6%E8%97%8F&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A3150040683%7D)，其实有点意料不到，多说一点吧
  

我只懂generate这一步，就先来说为什么其他框架这部分代码，相比于[llama2](https://www.zhihu.com/search?q=llama2&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A3150040683%7D)更复杂

过去，大模型出来的之前，大家对于[最优](https://www.zhihu.com/search?q=%E6%9C%80%E4%BC%98&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A3150040683%7D)的生成方式没有共识

最大化模型可能性的方法，就有greedy, beam search, stochastic beam search, diverse beam search……

单就这[diverse beam search](https://www.zhihu.com/search?q=diverse%20beam%20search&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A3150040683%7D)我就能找出七八种，若是找不出来，我现想也能给你想够八种

抽样的方法，就有sampling，temperature sampling，top-k/top-p sampling，nucleus sampling，以及几种方法的[排列组合](https://www.zhihu.com/search?q=%E6%8E%92%E5%88%97%E7%BB%84%E5%90%88&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A3150040683%7D)

除此之外，过去模型小，还得打一堆补丁。比如length penalty，repetition penalty，等等……

那么过去的框架，比如hugging_face，以及我更熟悉的[seq2seq](https://www.zhihu.com/search?q=seq2seq&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A3150040683%7D)是怎么办的呢？

当然是全写上，任君挑选

于是，generate简简单单就突破3000行

代码不好的同学如我，头发也就跟着垮垮掉

  

  

那llama 2为什么轻量化了呢？

在[大模型时代](https://www.zhihu.com/search?q=%E5%A4%A7%E6%A8%A1%E5%9E%8B%E6%97%B6%E4%BB%A3&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A3150040683%7D)，大家似乎在生成方式上达成了一个共识

那就是nucleus sampling，或者只有temperature

当然[chatgpt](https://www.zhihu.com/search?q=chatgpt&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A3150040683%7D)的内部代码[我看不到](https://www.zhihu.com/search?q=%E6%88%91%E7%9C%8B%E4%B8%8D%E5%88%B0&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A3150040683%7D)，我只是根据llama2的代码猜测的。说得肯定错的比对的多，希望大家指正。

背后的原因，我想有几条。

1.  beam search的成本随着beam数量的增加而增加，在过去模型小的时候inference的成本可以忽略不计，现在跑一次很贵。sampling没有额外成本。
2.  [sampling](https://www.zhihu.com/search?q=sampling&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A3150040683%7D)的生成效果更像人话，符合人类说话的distribution。但是如果[temperature](https://www.zhihu.com/search?q=temperature&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A3150040683%7D) 设为1，生成质量又太差了，所以还是调一下temperature，取个折中。
3.  [抽样方法](https://www.zhihu.com/search?q=%E6%8A%BD%E6%A0%B7%E6%96%B9%E6%B3%95&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A3150040683%7D)每次出来结果都不一样，满足用户对多样性的要求。如果用argmax类的方法想实现这一点，就得用那些diverse beam search 12345678，性能不好，主要是对[diversity](https://www.zhihu.com/search?q=diversity&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A3150040683%7D)的定义不明确也不自然

然后，随着模型质量的提升，也可能是抽样方法的先天优势，生成结果过短和重复的问题也解决了。过去打得补丁似乎也不需要了。

所以llama2的generate.py就剩300行了。

* * *

如果你想要实现你的beam search/diverse beam search/nucleus sampling/tree model sampling/[常温超导](https://www.zhihu.com/search?q=%E5%B8%B8%E6%B8%A9%E8%B6%85%E5%AF%BC&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A3150040683%7D)sampling/先天八卦 beam search，怎么办呢？

那就打开hugging_face的代码，一行行看懂了，抄进你的llama2的git里。再改。

抄代码这事，一天一百行不过分吧

你就也别[unit test](https://www.zhihu.com/search?q=unit%20test&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A3150040683%7D)了，大概也不会，直接边写边整个test

写一点test一点，肉眼debug

再复杂的方法，两三天也抄完了

不说对[算法](https://www.zhihu.com/search?q=%E7%AE%97%E6%B3%95&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A3150040683%7D)更理解，单就是自己写的屎那也比别人的[龙涎香](https://www.zhihu.com/search?q=%E9%BE%99%E6%B6%8E%E9%A6%99&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A3150040683%7D)要更容易看懂

然后该干什么干什么，跑实验做测试写文章，手工小作坊呗

科研嘛，不丢人

  

  

那你要问了，我花这功夫，写出来代码还不如hugging_face原装，为啥不直接用人家的呢？

别人的东西，用着爽，貌似改个东西只用加个几行

但是，[debug](https://www.zhihu.com/search?q=debug&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A3150040683%7D)起来可能要花2-3周，比自己写一遍多了好几倍的时间

3000多行啊，你想找到哪个地方出错了真不容易，还是自己的屎山香

那你又问，说如果我没遇到bug，就用人家的。遇到了再重写，不更好吗？

我的经验是，人都是有惰性的。给你现成的，你肯定下不定决心抛弃不用从头写。

不如开头就逼自己走歪门邪道


最后一个问题，如果这么屎的代码，附在论文后面，不会影响其他人吗？

其实大家也都各自有自己一坨屎山

你的代码也就是pull下来跑一跑最基本的demo

剩下的工作，还是会在自己的屎山上复现，不会在你之上改的

* * *

我低估hugging face了。它的model.generate至少3w行，里面可以设置的参数比这篇文章点赞还多

