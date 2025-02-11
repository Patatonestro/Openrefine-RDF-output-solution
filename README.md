**Fixing CSRF Token Error When Exporting RDF in OpenRefine**

Grefine-RDF 插件在 OpenRefine 中导出遇到 CSRF Token Error 解决方法

---

**📌 Quick Fix 一句话版本**

**I have uploaded the modified `menu-bar-extension.js` file. If you are using the **Grefine-RDF-extension** and encounter a 【missing or invalid CSRF Token Error】 when exporting RDF, simply **replace the `module/script/menu-bar-extension.js`** file with the one above, and the issue will be resolved!**

我已经上传了修改后的 `menu-bar-extension.js` 文件**，如果你使用 **Grefine-RDF插件**，并在导出 RDF 时遇到 `missing or invalid CSRF Token Error`报错，请把 `module/script/menu-bar-extension.js`文件替换成这里修改过的版本，应该能解决导出问题😊

---

## **🔍 Full Explanation 完整解决思路**
### 🛠️ Problem Description 问题描述 

**I could successfully model data with this extension, but encountered a `missing or invalid CSRF token` error when exporting.** 
**Only RDF format exports failed, while OpenRefine's built-in formats (CSV, TSV, Excel) exported without issues.**  
**This indicated that the issue was likely with the Grefine-RDF plugin rather than OpenRefine itself.**  

我可以正常用这个插件建模数据，但在导出时总是报 `missing or invalid CSRF token` 错误。
只有 RDF 格式的导出失败，而 OpenRefine 自带的其他格式（CSV、TSV、Excel）都可以正常导出。
这说明问题很可能出在 Grefine-RDF 插件本身，而不是 OpenRefine。

**Problem Analysis 问题定位**

🔹 1. Locating the Plugin's Export Logic 找到插件的导出逻辑:

The GRefine-RDF-extension’s export logic is located at: 
在 OpenRefine 的安装目录中，GRefine-RDF 插件的导出代码位于：  
```
module/script/menu-bar-extension.js
```
While OpenRefine’s **other export formats** are managed in:
而 OpenRefine 官方的 **其他格式导出代码** 在：  
```
/Applications/OpenRefine.app/Contents/Resources/webapp/modules/core/scripts/project/exporters.js
```
(*ppps: I am using Mac so the path may be different, but the file name should be the same.)

**Comparing the plugin’s export logic with OpenRefine’s built-in exporters, I found a difference：**
我将插件的导出代码和官方的代码进行了对比，发现了一个关键区别：
  - **OpenRefine’s official export logic first retrieves the CSRF token (`csrf_token`) and includes it in the request.**
  - **Since the server requires a CSRF token for security validation, The plugin, however, lacks any CSRF token handling, causing the export request to be rejected, thus result in a `missing or invalid CSRF Token Error`.**

  - OpenRefine 官方的导出逻辑会先获取 CSRF 令牌 (`csrf_token`) 并将其附加到请求中,服务器的安全机制需要 CSRF 令牌来验证请求，但插件的代码里没有获取 CSRF 令牌的逻辑，导致导出请求被服务器拒绝,这就是为什么导出会报错。

<img width="1466" alt="截屏2025-02-11 14 08 12" src="https://github.com/user-attachments/assets/a5a7f05a-3018-4176-83dd-e5fd6b236716" />


### **🔧 Solution 解决方案**
**I referenced OpenRefine’s output logic in the official code and added CSRF token handling.**
- **Integrated `Refine.wrapCSRF()` to correctly fetch the CSRF token.**  
- **Ensured the CSRF token is included in the export request.**  
我参考 OpenRefine 官方导出代码，添加 CSRF 令牌处理，在插件导出模块合并了 `Refine.wrapCSRF()` 逻辑，使插件能够正确获取 CSRF 令牌。
且确保 CSRF 令牌被附加到导出的 `POST` 请求中。


**🔹 修正后的代码（Fixed Code with CSRF Token Handling）：**
I only modified this part, here is the screenshot
<img width="888" alt="截屏2025-02-11 14 16 10" src="https://github.com/user-attachments/assets/5585d22a-449a-45e8-a5c4-175ec9a3e017" />

---

After fixing the issue, the GRefine-RDF plugin now correctly retrieves the CSRF token, preventing export errors.
🔥 **If you encounter the same issue, I hope this article helps you!** 

这样修改之后，RDF 格式（RDF/XML, Turtle）就都可以正常导出啦，如果你也遇到这个问题，希望这篇文章能帮到你！ 
