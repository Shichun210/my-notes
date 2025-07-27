



### 📌提交commit到指定的branch，举例ai50/projects/2024/x/degrees

&#x27A4;**新建一个branch名叫ai50/projects/2024/x/degrees**
`git checkout -b ai50/projects/2024/x/degrees`

➤**修改代码，在CS50_AI_python目录下只留下degrees目录并提交commit**
`git add .`
`git commit -m "Add project degrees"`
`git push origin ai50/projects/2024/x/degrees`

---
### 📌提交作业：

➤**移动到对应的目录**
`cd degrees`

➤**安装check50和submit50**
`pip install --upgrade pip`
`pip install check50 submit50`

➤**检查有没有安装好**
`check50 --version`
`submit50 --version`

➤**设置环境变量**


➤**如果token过期，重新生成**
> 右上角图标点击`➜`Settings`➜`Developer settings`➜`Personal access tokens`➜`Tokens(classic)`➜`点击你的token`➜`regenerate token

➤**然后就可以check和提交了**
`check50 ai50/projects/2024/x/degrees`
`Submit50 ai50/projects/2024/x/degrees`
