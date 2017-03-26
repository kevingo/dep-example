# dep-example
Sample repo for testing golang dependency tool dep

# 介紹

Dep 是 Golang 的一個 Dependency Management tool。長久以來 Golang 一直沒有一個大一統的相依管理工具，造成各個開發者使用各種不同的工具來管理 dependency，你會看到 golang 官方的 wiki 中就列出了一堆工具，請參考：[PackageManagementTools](https://github.com/golang/go/wiki/PackageManagementTools)。

而 Dep 是由 Peter Bourgon(go-kit 主要作者)、Jessie Frazelle(Google 工程師)、 Andrew Gerrand(Google Go team 成員) 和 Sam Boyer(gps 開發者) 等人共同開發，

目前 dep 正在 pre-alpha，而它目標是成為 Golang 官方的 dependency tool，希望會在 1.10 release 的時候正式 merge 進入 toolchain 當中，也許之後你就可以用 `go dep` 來管理套件了，有興趣的可以參考他們的 [roadmap](https://github.com/golang/dep/wiki/Roadmap)。

# 注意

由於現在 dep 還在開發階段，還有許多不確定性，比如說：
1. [https://github.com/golang/dep/issues/119](https://github.com/golang/dep/issues/119)：像是現在 dep 當中的 menifest 和 lock 檔案都是使用 JSON 格式，但未來正式推出時可能會改成 TOML。
2. [https://github.com/golang/dep/issues/168](https://github.com/golang/dep/issues/168)menifest 和 lock 兩個檔案命名是否會變更。

有興趣的可以追蹤一下 dep 的 issue：[https://github.com/golang/dep/issues?q=is%3Aopen+is%3Aissue+label%3Abefore-merge-into-toolchain](https://github.com/golang/dep/issues?q=is%3Aopen+is%3Aissue+label%3Abefore-merge-into-toolchain)

# 使用方式

儘管 dep 還有許多的不確定性，我們還可以來看看這個未來可能會變成官方套件管理工具的 dep 要怎麼用。

## menifest

## lock

## dep init

## dep ensure

## dep update

## dep remove

