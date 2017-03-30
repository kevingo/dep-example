# dep - Golang Dependency Management Tool

Dep 是 Golang 的一個 Dependency Management tool。長久以來 Golang 一直沒有一個大一統的相依管理工具，造成各個開發者使用各種不同的工具來管理專案的 dependency，你會看到 golang 官方的 wiki 中就列出了一堆工具，請參考：[PackageManagementTools](https://github.com/golang/go/wiki/PackageManagementTools)。

而在 Edward Muller 分享的 [State of Go 2016](http://go-talks.appspot.com/github.com/freeformz/talks/20160712_gophercon/talk.slide#1) 中也看到大家常用的套件管理工具真是各式各樣：

![image](https://raw.githubusercontent.com/kevingo/dep-example/master/screenshot/dependency.png)

而 Dep 是由 Peter Bourgon(go-kit 主要作者)、Jessie Frazelle(Google 工程師)、 Andrew Gerrand(Google Go team 成員) 和 Sam Boyer(gps 開發者) 等人共同開發。目前 dep 正在 pre-alpha，而它目標是成為 Golang 官方的 dependency tool，希望會在 1.10 release 的時候正式 merge 進入 toolchain 當中，也許之後你就可以用 `go dep` 來管理套件了，有興趣的可以參考他們的 [roadmap](https://github.com/golang/dep/wiki/Roadmap)。

# 注意

由於現在 dep 還在開發階段，整個專案還不是很穩定，比如說下面幾個 issue：

1. [https://github.com/golang/dep/issues/119](https://github.com/golang/dep/issues/119)：現在 dep 當中的 menifest 和 lock 檔案都是使用 JSON 格式，但未來正式推出時很有可能改成 TOML。
2. [https://github.com/golang/dep/issues/168](https://github.com/golang/dep/issues/168)：manifest 和 lock 兩個檔案命名是否會變更。

有興趣的可以追蹤一下 dep 的 issue 清單：[https://github.com/golang/dep/issues?q=is%3Aopen+is%3Aissue+label%3Abefore-merge-into-toolchain](https://github.com/golang/dep/issues?q=is%3Aopen+is%3Aissue+label%3Abefore-merge-into-toolchain)

# 使用方式

儘管 dep 整個專案還不是很穩定，我們還可以來看看這個未來可能會變成官方套件管理工具要怎麼使用。

## dep init
dep init 會解析你的 `$GOPATH`，並把你用到的套件加入到 manifest.json 中。要注意的是，目前 dep 的行為會去檢查你引用的套件是否存在於 `$GOPATH` 中，如果有的話才會被加入到 manifest.json 中，可見原始碼 [init.go#L302](https://github.com/golang/dep/blob/1b193f4439655572d59fe1b87035870f1a7344ca/init.go#L302)。

而 lock.json 中則是會記錄完整的相依關係，包含引用套件版本的 commit SHA 和 dependencies graph 都會被記錄下來。

```
{
    "memo": "f629e3ef907b4b946a6e5070c6728214734d69becd4b2065611efaf6d93ae47d",
    "projects": [
        {
            "name": "github.com/gorilla/context",
            "version": "v1.1",
            "revision": "1ea25387ff6f684839d82767c1733ff4d4d15d0a",
            "packages": [
                "."
            ]
        },
        {
            "name": "github.com/gorilla/mux",
            "version": "v1.3.0",
            "revision": "392c28fe23e1c45ddba891b0320b3b5df220beea",
            "packages": [
                "."
            ]
        }
    ]
}
```

## dep ensure
dep ensure 則是在你想要確保所有的 dependencies 都被記錄下來時，就可以使用 `dep ensure` 指令，或是你想要指定某套件的版本，比如說我想要使用 mux 套件的 1.2.x 版本，就可以透過 `dep ensure github.com/gorilla/mux@~1.2.0` 的指令，dep 會幫你更新 manifest.json 和 lock.json 兩個檔案，同時更新 `vendor/` 目錄下的相關套件，請見 [此 commit](https://github.com/kevingo/dep-example/commit/3d04fe77781d449620f682e66341f1a21d4177a0)。

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

其他的功能請參考 help。不過 status 的開發還不是很完整，所以很多指令你嘗試後發現都會是一樣的結果。這部分可能也要再等等正式的版本推出了。

```bash
Usage: dep status [package...]

With no arguments, print the status of each dependency of the project.

  PROJECT     Import path
  CONSTRAINT  Version constraint, from the manifest
  VERSION     Version chosen, from the lock
  REVISION    VCS revision of the chosen version
  LATEST      Latest VCS revision available
  PKGS USED   Number of packages from this project that are actually used

With one or more explicitly specified packages, or with the -detailed flag,
print an extended status output for each dependency of the project.

  TODO    Another column description
  FOOBAR  Another column description

Status returns exit code zero if all dependencies are in a "good state".

Flags:

  -detailed  report more detailed status (default: false)
  -dot       output the dependency graph in GraphViz format (default: false)
  -f         output in text/template format (default: <none>)
  -json      output in JSON format (default: false)
  -missing   only show missing dependencies (default: false)
  -modified  only show modified dependencies (default: false)
  -old       only show out-of-date dependencies (default: false)
  -unused    only show unused dependencies (default: false)
  -v         enable verbose logging (default: false)
```

另外要注意的是如果你有任何 missing 或未使用的套件，執行 `dep status` 時都會先提示。

## dep remove

`dep remove` 會把 dependencies 從 manifest.json、lock.json 和 vendor 目錄中移除，請參考[此 commit](https://github.com/kevingo/dep-example/commit/28f5371615a0b65ffb22ab3f639316bf19d6a4c1)。

如果你的程式碼中還有引用到這些套件時， dep 也會提示，你也可以使用 `-force` 指令來強制移除。
 
```bash
$ dep remove github.com/gorilla/mux
not removing "github.com/gorilla/mux" because it is imported by "github.com/kevingo/dep-example" (pass -force to override)
```