# 获取光标的位置

实现该功能，需要使用一个GUI库和 `robotgo` 的子项目 `gohook`。

该程序的核心是点击按钮进入阻塞状态，等待鼠标点击，点击之后获得对应位置的坐标
<!--more-->
## 下载gohook
```bash
go get github.com/robotn/gohook
```
注意：不要同时使用`robotgo` 和 `gohook`，`robotgo` 所使用的 `gohook` 是旧版本

## 编写GUI代码
> 我选择的GUI库是fyne

读取配置文件代码如下
```go
// config.go
package config

import (
	"log"
	"os"
	"strings"

	"github.com/flopp/go-findfont"
	"github.com/spf13/viper"
)

func init() {
	// 读取配置文件
	viper.SetConfigName("config")
	viper.AddConfigPath(".")
	if err := viper.ReadInConfig(); err != nil { // 读取配置文件
		log.Fatalf("Fatal error config file: %s \n", err)
	}

	//设置中文字体
	fontPaths := findfont.List()
	for _, path := range fontPaths {
		if strings.Contains(path, "msyh.ttf") || strings.Contains(path, "simchei.ttf") || strings.Contains(path, "simsun.ttc") || strings.Contains(path, "simkai.ttf") {
			os.Setenv("FYNE_FONT", path)
			break
		}
	}
}
```
正文代码如下
```go
// main.go
package main

import (
	"fmt"
	"log"
	"os"

	_ "pos/config"

	"fyne.io/fyne/v2/app"
	"fyne.io/fyne/v2/container"
	"fyne.io/fyne/v2/widget"
	"github.com/lxn/win"
	hook "github.com/robotn/gohook"
)

func main() {
	// 打开日志文件
	file, err := os.OpenFile("cursor.log", os.O_RDWR|os.O_CREATE|os.O_APPEND, 0666)
	if err != nil {
		log.Fatal(err)
	}
	// 创建logger
	consolelog := log.New(os.Stdout, "[Debug]", log.LstdFlags|log.Lshortfile) // 设置[Debug]日志输出到控制台
	filelog := log.New(file, "[Info]", log.LstdFlags|log.Lshortfile) // 设置[Info]日志输出到文件,Info日志包含获取的坐标

	a := app.New() // 创建Fyne程序
	w := a.NewWindow("获取光标位置") // 创建窗口
	title := widget.NewLabel("点击获取坐标") // 设置显示的文字
	list1 := widget.NewLabel("1. 点击Click Me按钮")
	list2 := widget.NewLabel("2. 在想要获取坐标的位置左击")
	list3 := widget.NewLabel("3. 坐标会保存在log中")

	var p win.POINT // 能不能点击按钮进入阻塞，等待鼠标点击呢，点击之后就能获得坐标了
	btn := widget.NewButton("Click me", func() {
		consolelog.Print("点击了按钮")
		consolelog.Print("开始监听鼠标事件")
		if ok := hook.AddEvent("mleft"); ok { // 监听鼠标左键事件
			consolelog.Print("监听鼠标左键事件成功")
			if ok := win.GetCursorPos(&p); !ok { // 获取鼠标位置
				consolelog.Print("获取鼠标位置失败")
			}
			filelog.Print(fmt.Sprintf("X=%d,Y=%d", p.X, p.Y))
		}
	})
	textContainer := container.NewGridWithRows(4, title,  list1, list2, list3)  // 创建容器
	w.SetContent(container.NewVBox(textContainer, btn)) // 设置界面内容
	w.ShowAndRun() // 显示界面并运行
}
```

