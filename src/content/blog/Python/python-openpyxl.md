---
author: facsert
pubDatetime: 2022-09-07 15:28:43
title: Python openpyxl
postSlug: ""
featured: false
draft: false
tags:
  - Python
  - openpyxl
description: "Python Excel 表格模块 openpyxl"
---

<!--
 * @Author       : facsert
 * @Date         : 2022-09-07 15:28:43
 * @LastEditTime: 2023-09-27 21:27:36
 * @Description  : edit description
-->

# openpyxl

python excel 模块

## 安装

```bash
 $ pip install openpyxl
 $ pip list | grep openpyxl
 > openpyxl                      3.0.10
```

## 创建读取

### 创建表

```python
 from openpyxl import Workbook

 wb = Workbook()                                 # 创建一个工作表
 sheet = wb.active                               # 激活第一个 sheet
 sheet['A1'] = 'hellow world'                    # sheet A1 赋值
 wb.save('test.xlsx')                            # 保存 excel 文件为 test.xlsx
 wb.close()                                      # 关闭工作表


 wb.create_sheet(name:str [, index:int = -1])    # 自定义 sheet 名称和位置
 wb.create_sheet('first_sheet', 0)               # 在 0 插入 名为 first_sheet 的表
 sheet.title                                     # 获取sheet 的名称, 也可以重新赋值
 > first_sheet

 for sheet in wb:                                # 遍历工作表, 打印所有表名称
    print(sheet.title)

 wb.remove(sheet)                                # 删除 sheet, 参数是 sheet 对象
```

### 读取表

```python
 from openpyxl import load_workbook

 wb = load_workbook('test.xlsx')                 # 打开一个已存在的 excel 文件
 sheet_names = wb.sheetnames                     # 获取所有表名组成的列表
 > ['first', 'second', 'third']

 sheet = wb['first']
 sheet['A1']
 > 'hellow world'
```

## 读写

### 赋值和读取

```python
 sheet['A1'] = 'hellow world'                    # A1 赋值 hellow world
 sheet['A1'].value                               # 读取 A1 方格的值
 > 'hellow world'

 sheet.cell(row=1, col=1).value = 'value'        # 行 1, 列 1 赋值 value
 grid = sheet.cell(row=1, column=1, value='ok')  # 第一列第一行赋值 ok, 返回一个 cell 对象
 sheel.cell(1, 1).value                          # 读取第一行第一列方格的值
 > 'ok'

 row_one = sheet['A1': 'F1']                     # 获取多数方格, 返回 cell 对象的元组
 sheet.max_column                                # 含有数据结束列
 sheet.append([1, 2, 3, 4])                      # 添加一行数据
```

### 多行遍历

- sheet.iter_rows() 每次返回一整行组成的元组
- sheet.iter_cols() 每次返回一整列组成的元组

|     参数      |                 含义                 |
| :-----------: | :----------------------------------: |
|   `min_row`   |         起始行, default:None         |
|   `max_row`   |         结束行, default:None         |
|   `min_col`   |         起始列, default:None         |
|   `max_col`   |         结束列, default:None         |
| `values_only` | True:只返回值, False: 返回 cell 对象 |

```python

 for row in sheet.iter_rows(
    min_row=1, max_col=3, max_row=2):            # 遍历第 1 至第 2 行, 取 3 列
    for cell in row:
        print(cell)

 <Cell Sheet1.A1>
 <Cell Sheet1.B1>
 <Cell Sheet1.C1>
 <Cell Sheet1.A2>
 <Cell Sheet1.B2>
 <Cell Sheet1.C2>

 for col in sheet.iter_cols(
    min_row=1, max_col=3, max_row=2):            # 遍历第 1 至第 2 行, 取 3 列
    for cell in col:
        print(cell)

<Cell Sheet1.A1>
<Cell Sheet1.A2>
<Cell Sheet1.B1>
<Cell Sheet1.B2>
<Cell Sheet1.C1>
<Cell Sheet1.C2>
```

## 样式

### 字体

```python
 from openpyxl.styles import Font

 cell.font = Font(
   name="微软雅黑",                                  # 字体
   size=15,                                         # 字体大小
   color="0000FF",                                  # 字体颜色，用16进制rgb表示
   bold=True,                                       # 是否加粗，True/False
   italic=True,                                     # 是否斜体，True/False
   strike=None,                                     # 是否使用删除线，True/False
   underline=None,                                  # 下划线, 可选'singleAccounting', 'double', 'single', 'doubleAccounting'
 )

```

### 填充颜色

```python
 from openpyxl.styles import PatternFill

 cell.fill = PatternFill(
    patternType="solid",                         # 填充类型
    fgColor="F562a4",                            # 前景色，16进制rgb
    bgColor="0000ff",                            # 背景色，16进制rgb
 )

 patternType:                                    # patternType 可选值
     none、solid、darkGray、mediumGray、lightGray、
     lightDown、lightGray、lightGrid
```

### 边框

```python
 from openpyxl.styles import Border, Side

 side = Side(
   style="medium",                               # 边框样式
   color="ff66dd",                               # 边框颜色，16进制rgb表示
 )

 style:                                          # style 可选值
     dashDot、dashDotDot、dashed、dotted、double、
     hair、medium、mediumDashDot、mediumDashDotDot、
     mediumDashed、slantDashDot、thick、thin

 cell.border = Border(
    top=side,                                    # 上边框使用定义的 side
    bottom=side,                                 # 下边框使用定义的 side
    left=side,                                   # 左边框使用定义的 side
    right=side,                                  # 右边框使用定义的 side
    diagonal=side                                # 对角线使用定义的 side
)

```

### 对齐

```python
 from openpyxl.styles import Alignment

 cell.alignment = Alignment(
    horizontal='left',                           # 水平对齐，可选general、left、center、right、fill、justify、centerContinuous、distributed
    vertical='top',                              # 垂直对齐， 可选top、center、bottom、justify、distributed
    text_rotation=0,                             # 字体旋转，0~180整数
    wrap_text=False,                             # 是否自动换行
    shrink_to_fit=False,                         # 是否缩小字体填充
    indent=0,                                    # 缩进值
)
```

### 示例

```py
class Excel:

    def __init__(self, file, mode):
        self.file = file
        self.mode = mode
        self.mode_init()

    def __enter__(self):
        return self

    def __exit__(self, e_type, e_value, e_tb):
        self.wb.close()
        if any((e_type, e_value, e_tb)):
            raise RuntimeError(f"error: {e_value}\n")

    def mode_init(self):
        """ 模式初始化
            'w' 新建 excel 表格
            'r' 读取 excel 表格
        """
        if self.mode.lower() == 'w':
            self.wb = Workbook()
            self.sheet = self.wb.active
            return

        if self.mode.lower() == 'r':
            self.wb = load_workbook(self.file, data_only=True)
            self.sheet = self.wb[self.wb.sheetnames[0]]
            self.head = self.read_head()
            return

        print(f'error mode {self.mode}, select r or w')
        exit()

    def cell_value(self, row, col):
        """ 通过坐标读取值 """
        return self.sheet.cell(row=row, column=col).value

    def set_cell(self, row, col, value):
        """ 通过坐标写入值 """
        self.sheet.cell(row, col).value = value

    def read_head(self):
        """ 读取表格表头 """
        max_col = self.sheet.max_column + 1
        return [self.cell_value(1, col) for col in range(1, max_col)]

    def select_column(self, select, key=None):
        """ 选择属性对应的列
            select 筛选需要输出的列

        """
        if len(select) == 0:
            select = self.head

        if key != None and key not in select:
            print(f"{key} not in {select}")
            exit()

        if set(self.head) < set(select):
            print(f'{select} not in {self.head}')
            exit()

        return [self.head.index(k) for k in select if k in self.head]

    def excel_to_list(self, select=[]):
        """ 读取表格生成列表
            select 筛选需要读取的列
        """
        indexs = self.select_column(select)
        excel_list = []

        for row in range(2, self.sheet.max_row + 1):
            excel_list.append({
                self.head[col]: self.cell_value(row, col+1)
                for col in indexs
            })
        return excel_list

    def excel_to_dict(self, key, select=[]):
        """ 读取表格生成字典, 一行一个字典
            key 指定改行转成字典的 key
            select 筛选需要输出的键值对
        """
        indexs = self.select_column(select, key)
        excel_dict = {}

        for row in range(2, self.sheet.max_row + 1):
            key_value = self.cell_value(row, self.head.index(key) + 1)
            excel_dict.update({key_value: {
                self.head[col]: self.cell_value(row, col+1)
                for col in indexs
            }})

        return dict(sorted(excel_dict.items()))

    def list_to_excel(self, lst, select=[]):
        """ 列表生成表格
            lst list[dict]: 字典列表
            select 筛选需要输出的列
        """
        try:
            self.head = list(lst[0].keys())
        except Exception as e:
            print(f"data type error, must list[dict]")
            exit()

        indexs = self.select_column(select)
        self.sheet.append([self.head[i] for i in indexs])
        for line in lst:
            self.sheet.append([line[self.head[i]] for i in indexs])

        self.wb.save(self.file)

    def dict_to_excel(self, dic, select=[]):
        """字典生成表格
           dic dict[dict]: 双层字典
           select 需要写入表格的键值对
        """
        try:
            self.head = list(list(dic.values())[0].keys())
        except Exception as e:
            print(f"data type error, must dict[str, dict]")
            exit()

        indexs = self.select_column(select)
        self.sheet.append([self.head[i] for i in indexs])
        for line in dic.values():
            self.sheet.append([line[self.head[i]] for i in indexs])

        self.wb.save(self.file)
```
