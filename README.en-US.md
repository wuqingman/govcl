* [中文](README.md)   
* English  

----

## Cross-platform Golang GUI library. Use Delphi VCL and Lazarus LCL for binding.

> The govcl version >=1.2.0 must require the go version >=1.9.0.  

[Screenshots](https://github.com/ying32/govcl/tree/master/Screenshot) | 
[What's-new](https://z-kit.cc/changelog.html) | 
[GoVCL video tutorial (third party)](https://video.0-w.cc/videos/1) | 
[Sponsor govcl](https://z-kit.cc/sponsor.html)  

----

### Support Platform    
Windows | Linux | macOS  

> Note: linux and macOS only part of the components, properties, events and methods are valid.  
> If you want to support linux arm and linux 32bit, you need to compile the corresponding liblcl binary.   


### Pre-compiled GUI library binary download     
[![Librarys](https://img.shields.io/github/downloads/ying32/govcl/latest/Librarys-1.2.9.zip.svg)](https://github.com/ying32/govcl/releases/download/v1.2.9/Librarys-1.2.9.zip)  
> Note: The "libvcl" library binary (libvcl. dll, libvclx64. dll) contained in the compression package is only for preview and test purposes. Please compile the "libvcl" source code for official use. please refer to the instructions in [UILIbSrcources](UILibSources/README.en-US.md).   

### res2go Tool([doc](Tools/res2go))    
[![res2go](https://img.shields.io/badge/downloads-res2go%201.0.19-blue.svg)](https://github.com/ying32/govcl/releases/download/v1.2.9/res2go-1.0.19.zip)  
> Note: Designed in Delphi/Lazarus, code written in Golang.  

[GoVCL extension package](https://github.com/ying32/exts)  
> Note: The inc or pas file in the extension package needs to be compiled into the libvcl/liblcl directory.     

 

### usage: 

#### Step 1: Get the govcl code  

> go get -u github.com/ying32/govcl    

#### Step 2: Write the code

* Method 1(Use Delphi / Lazarus or GoVCLDesigner to design the GUI. recommend): 

```golang
package main


import (
   "github.com/ying32/govcl/vcl"
   // Do not reference this package if you use custom syso files
   _ "github.com/ying32/govcl/pkgs/winappres"
)

type TMainForm struct {
    *vcl.TForm
    Btn1     *vcl.TButton
}

type TAboutForm struct {
    *vcl.TForm
    Btn1    *vcl.TButton
}

var (
    mainForm *TMainForm
    aboutForm *TAboutForm
)

func main() {
    vcl.Application.Initialize()
    vcl.Application.SetMainFormOnTaskBar(true)
    vcl.Application.CreateForm(&mainForm)
    vcl.Application.CreateForm(&aboutForm)
    vcl.Application.Run()
}

// -- TMainForm

func (f *TMainForm) OnFormCreate(sender vcl.IObject) {
    
}

func (f *TMainForm) OnBtn1Click(sender vcl.IObject) {
    vcl.ShowMessage("Hello!")
}

// -- TAboutForm

func (f *TAboutForm) OnFormCreate(sender vcl.IObject) {
 
}

func (f *TAboutForm) OnBtn1Click(sender vcl.IObject) {
    vcl.ShowMessage("Hello!")
}
```
**Method 1 needs to be used in conjunction with the UI designer or the res2go tool.**  


* Method 2(Pure code, imitating the way of Delphi class, can automatically bind events.):  

```golang
package main


import (
   "github.com/ying32/govcl/vcl"
   // Do not reference this package if you use custom syso files
   _ "github.com/ying32/govcl/pkgs/winappres"
)

type TMainForm struct {
    *vcl.TForm
    Btn1     *vcl.TButton
}

type TAboutForm struct {
    *vcl.TForm
    Btn1    *vcl.TButton
}

var (
    mainForm *TMainForm
    aboutForm *TAboutForm
)

func main() {
    vcl.Application.Initialize()
    vcl.Application.SetMainFormOnTaskBar(true)
    vcl.Application.CreateForm(&mainForm)
   // Bind subcomponent events after creation.
    vcl.Application.CreateForm(&aboutForm, true)
    vcl.Application.Run()
}

// -- TMainForm

func (f *TMainForm) OnFormCreate(sender vcl.IObject) {
    f.SetCaption("Hello")
    f.Btn1 = vcl.NewButton(f)
    f.Btn1.SetParent(f)
    f.Btn1.SetBounds(10, 10, 88, 28)
    f.Btn1.SetCaption("Button1")
    f.Btn1.SetOnClick(f.OnButtonClick)  
}

func (f *TMainForm) OnButtonClick(sender vcl.IObject) {
    vcl.ShowMessage("Hello!")
}


// -- TAboutForm

func (f *TAboutForm) OnFormCreate(sender vcl.IObject) {
    f.SetCaption("Hello")
    f.Btn1 = vcl.NewButton(f)
    //f.Btn1.SetName("Btn1")
    f.Btn1.SetParent(f)
    f.Btn1.SetBounds(10, 10, 88, 28)
    f.Btn1.SetCaption("Button1")
}

func (f *TAboutForm) OnBtn1Click(sender vcl.IObject) {
    vcl.ShowMessage("Hello!")
}
```


* Method 3(Pure code. Not recommended): 

```golang
package main

import (
   "github.com/ying32/govcl/vcl"
   // Do not reference this package if you use custom syso files
   _ "github.com/ying32/govcl/pkgs/winappres"
)

func main() {
    vcl.Application.Initialize()
    mainForm := vcl.Application.CreateForm()
    mainForm.SetCaption("Hello")
    mainForm.EnabledMaximize(false)
    mainForm.ScreenCenter()
    btn := vcl.NewButton(mainForm)
    btn.SetParent(mainForm)
    btn.SetCaption("Hello")
    btn.SetOnClick(func(sender vcl.IObject) {
        vcl.ShowMessage("Hello!")
    })
    vcl.Application.Run()
}
```  

#### Step 3: Copy the corresponding binary   

* Windows: According to whether the compiled binary is 32 or 64 bit, copy the corresponding "libvcl.dll" or "libvclx64.dll" or "liblcl.dll" to the current exe directory or system environment path.  
  * Go environment variable: `GOARCH = amd64 386` `GOOS = windows` `CGO_ENABLED=0`    

* Linux: Copy the "liblcl.so" executable directory (you can also copy liblcl.so to the `/usr/lib/` or `/usr/lib/x86_64-linux-gnu/` directory and use it as a public library).  
  * Go environment variable: `GOARCH = amd64` `GOOS = linux` `CGO_ENABLED=1`  

* MacOS: Copy the "liblcl.dylib" executable directory (Note for MacOS: you need to create the info.plist file yourself), or refer to: [App packaging on MacOS](https://gitee.com/ying32/govcl/wikis/pages?sort_id=410056&doc_id=102420)  
  * Go environment variable: `GOARCH = amd64` `GOOS = darwin` `CGO_ENABLED=1`  
---  
### Note:  

**When using the "liblcl" library, it is run in a compatible "libvcl" library, so some methods and properties of components and components are not available.**  

---   

**Special Note: All UI components are non-threaded/non-coroutine safe. When used in goroutine, use `vcl.ThreadSync` to synchronize updates to the UI.**

---

### FAQ

Q: Why is there no English WIKI?   
A: My English is bad. You can try using Google Translate [Chinese WIKI](https://gitee.com/ying32/govcl/wikis/pages).    
 
---  

### API document

* [Delphi VCL component document  WIKI](http://docwiki.embarcadero.com/RADStudio/Tokyo/en/Category:VCL_Reference)  
* [Lazarus LCL component document  WIKI](http://wiki.freepascal.org/LCL_Components)  
* [Windows API document](https://msdn.microsoft.com/zh-cn/library/ms123401.aspx)

----
