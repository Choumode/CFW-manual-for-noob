# Clash for Windows 新手使用教程
> ## 写在前面：
> 我算是小白，很多软件就只能马马虎虎用着，所以之前用SSR用着用着出问题了在网上又搜不到解决方案，无奈之下只得试试之前用过一小会儿但没用懂的Clash。所幸Clash的流行程度决定了网上能找到的教程非常多，一路跟着看下来也算是满足了自己的需求。为了避免长时间不用导致遗忘，就写个教程放在这里自用，如果能帮助到刚接触Clash的小白也是好的。

## **1. 中文支持**
Clash本身没有中文，但是可以通过替换文件来达到目的  
这是Github上的首选项 <https://github.com/BoyceLig/Clash_Chinese_Patch>  
跟着教程走就完事了
## **2. 自定义分流规则**
这是我最大的需求，但是在网上查了好几次都找不到太详细的教程（也可能是我搜索姿势不对）  
后来折腾一下午终于弄好了  
教程**只有简略版的分流规则**，如果想针对不同节点执行不同策略的还请看其他教程  

### **2.1 代码**
点击 <kbd>Settings 设置</kbd> 按钮 -> 滚动至 <kbd>Profiles 配置</kbd> 栏 -> 点击 <kbd>Parsers 预处理配置</kbd> 按钮  
进入编辑器，初始代码为 `parsers: # array`  
接下来根据自己的需求编写代码
```yaml
parsers: # array
  - reg: ^.*$
    yaml:
      prepend-rules:
        - DOMAIN-SUFFIX,google.com,🚀 节点选择
        - DOMAIN-SUFFIX,music.apple.com,🚀 节点选择
        - RULE-SET,reject,🛑 全球拦截
        - RULE-SET,proxy,🚀 节点选择
        - RULE-SET,direct,🎯 全球直连
        - GEOIP,LAN,🎯 全球直连
        - GEOIP,CN,🎯 全球直连
        - MATCH,🚀 节点选择
      
      mix-rule-providers:
        reject:
          type: http
          behavior: domain
          url: "https://raw.githubusercontent.com/Loyalsoldier/clash-rules/release/reject.txt"
          path: ./ruleset/reject.yaml
          interval: 86400
          
        proxy:
          type: http
          behavior: domain
          url: "https://raw.githubusercontent.com/Loyalsoldier/clash-rules/release/proxy.txt"
          path: ./ruleset/proxy.yaml
          interval: 86400

        direct:
          type: http
          behavior: domain
          url: "https://raw.githubusercontent.com/Loyalsoldier/clash-rules/release/direct.txt"
          path: ./ruleset/direct.yaml
          interval: 86400
```
> `reg` 表示对应的 订阅/配置文件，利用正则表达式匹配。  
 也可以用 `url` 来对应单个配置文件，  
 例如：`- url: https://example.com/profile.yaml` 表示配置中的 profile.yaml 文件采用预处理配置。

> `prepend-rules` 表示在规则的最前方插入规则，分流规则是从前到后执行，也就是说可以用前面的规则覆盖掉后面的规则；而 `append-rules` 则表示在最后方插入规则。  
 `- DOMAIN-SUFFIX,google.com,🚀 节点选择` 表示域名为 `google.com` 的网站走 `🚀 节点选择` 策略组，这个策略组是你的订阅商向你提供的。

> 如果所有的规则都需要自己写那太麻烦了，因此在 `mix-rule-providers` 这里就可以订阅大佬写好的文件，我用的是 [Loyalsoldier/clash-rules](https://github.com/Loyalsoldier/clash-rules) 的规则集。  
`reject\proxy\direct` 为规则集的名称，你可以自定义。  
`url` 是该规则集的网址，注意网址都是 `raw.githubusercontent.com` 才正确；如果是本地文件的话，则将 `url` 改为 `file`，后面填写文件的绝对路径即可。  
`interval` 是指该规则集多久更新一次，以毫秒为单位，这里的 86400毫秒 就等于 14.4分钟。

> 配置好远程规则集后还需要在 `pretend-rules` 中配置分流规则，上面代码中的 `- RULE-SET,reject,🛑 全球拦截` / `- RULE-SET,proxy,🚀 节点选择` / `- RULE-SET,direct,🎯 全球直连` 就是我设置的分流规则，表示reject规则集内的网址全部拦截，proxy规则集的网址走代理，direct规则集的网址直连。

具体解释可参考[官方文档-多处理器及正则匹配](https://docs.cfw.lbyczf.com/contents/parser.html#%E5%A4%9A%E5%A4%84%E7%90%86%E5%99%A8%E5%8F%8A%E6%AD%A3%E5%88%99%E5%8C%B9%E9%85%8D)

### **2.2 加载到订阅文件中**
点击 <kbd>Settings 设置</kbd> 按钮 -> 滚动至 <kbd>Profiles 配置</kbd> 栏 -> 点击 <kbd>更新订阅后直接切换为该配置文件</kbd> 按钮  
点击 <kbd>Profiles 配置</kbd> 按钮，更新你使用的订阅文件，你的预处理配置将会载入到订阅的配置文件中，完成！  

⚠️ 但是我此时遇到了一个情况，它报错了，说什么连接超时啥的，在issues里看了一圈，好多人都有这样的问题，有些人的解决方案是下载到本地使用，我采用的是用代理加速方案，这里用到的是 [Github Proxy](https://ghproxy.com/)，只需要在网址前面加上 `https://ghproxy.com/` 即可，例如 `https://ghproxy.com/https://raw.githubusercontent.com/Loyalsoldier/clash-rules/release/reject.txt`，完成！

## **3. 进阶教程**
这里列出一些看到的进阶教程  
<https://github.com/Fndroid/clash_for_windows_pkg/issues/2193>
