# dep - Golang Dependency Management Tool

Sample repo for testing golang dependency tool dep

# 介紹

Dep 是 Golang 的一個 Dependency Management tool。長久以來 Golang 一直沒有一個大一統的相依管理工具，造成各個開發者使用各種不同的工具來管理 dependency，你會看到 golang 官方的 wiki 中就列出了一堆工具，請參考：[PackageManagementTools](https://github.com/golang/go/wiki/PackageManagementTools)。

而 Dep 是由 Peter Bourgon(go-kit 主要作者)、Jessie Frazelle(Google 工程師)、 Andrew Gerrand(Google Go team 成員) 和 Sam Boyer(gps 開發者) 等人共同開發，

目前 dep 正在 pre-alpha，而它目標是成為 Golang 官方的 dependency tool，希望會在 1.10 release 的時候正式 merge 進入 toolchain 當中，也許之後你就可以用 `go dep` 來管理套件了，有興趣的可以參考他們的 [roadmap](https://github.com/golang/dep/wiki/Roadmap)。

# 注意

由於現在 dep 還在開發階段，還有許多不確定性，比如說：
1. [https://github.com/golang/dep/issues/119](https://github.com/golang/dep/issues/119)：像是現在 dep 當中的 menifest 和 lock 檔案都是使用 JSON 格式，但未來正式推出時可能會改成 TOML。
2. [https://github.com/golang/dep/issues/168](https://github.com/golang/dep/issues/168)：manifest 和 lock 兩個檔案命名是否會變更。

有興趣的可以追蹤一下 dep 的 issue：[https://github.com/golang/dep/issues?q=is%3Aopen+is%3Aissue+label%3Abefore-merge-into-toolchain](https://github.com/golang/dep/issues?q=is%3Aopen+is%3Aissue+label%3Abefore-merge-into-toolchain)

# 使用方式

儘管 dep 還有許多的不確定性，我們還可以來看看這個未來可能會變成官方套件管理工具的 dep 要怎麼用。

## dep init
dep init 會解析你的 $GOPATH，並把你用到的 dependencies 加入到 manifest.json 中。而 lock.json 中則是會記錄完整個 dependencies，包含引用套件版本的 commit SHA 都會被記錄。

## dep ensure
dep ensure 則是在你的專案有任何新的引用套件時，你想要確保所有的 dependencies 都被記錄下來時，就可以使用 `dep ensure` 指令。你也可以指定套件的版本，比如說我想要使用 mux 套件的 1.2.x 版本，就可以透過 `dep ensure github.com/gorilla/mux@~1.2.0` 的指令，dep 會幫你更新 manifest.json 和 lock.json 兩個檔案，同時更新 `vendor/` 目錄下的相關套件，請見 [此 commit](https://github.com/kevingo/dep-example/commit/3d04fe77781d449620f682e66341f1a21d4177a0)。

如果你想要讓 dependencies 只鎖定大版號，可以用 `dep ensure github.com/gorilla/mux@^1.2.0`，這樣的話版本的版號會被鎖定在 `>= 1.2.0 , < 2.0.0` 之間。其他更多關於 `dep ensure` 的使用方法，可以用 `dep ensure -examples` 來閱讀 Help。

## dep status
dep status 可以讓用來檢視目前專案 dependencies 的狀態，如下所示：

```bash
$ dep status
PROJECT                     CONSTRAINT  VERSION  REVISION  LATEST  PKGS USED
github.com/gorilla/context  *           v1.1     a85d2e5   v1.1    1
github.com/gorilla/mux      ~1.2.0      v1.2.0   b128961   v1.2.0  1
```

一些額外的指令如下：

- `$ dep status -missing`：用來檢視目前尚未加入 dep 管理的套件。

```bash
PROJECT                    MISSING PACKAGES`
github.com/armon/go-radix  [github.com/armon/go-radix]
```

另外要注意的是如果你有任何 missing 或未使用的套件，執行 `dep status` 時都會先提示。

## dep remove

`dep remove` 會把 dependencies 從 manifest.json、lock.json 和 vendor 目錄中移除，請參考[此 commit](https://github.com/kevingo/dep-example/commit/28f5371615a0b65ffb22ab3f639316bf19d6a4c1)。

如果你的程式碼中還有引用到這些套件時， dep 也會提示，你也可以使用 `-force` 指令來強制移除。
 
```bash
$ dep remove github.com/gorilla/mux
not removing "github.com/gorilla/mux" because it is imported by "github.com/kevingo/dep-example" (pass -force to override)
```