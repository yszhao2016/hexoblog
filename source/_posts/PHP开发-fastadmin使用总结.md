---
title: PHP开发-fastadmin使用总结
categories:
  - PHP开发
abbrlink: ae0c2972
date: 2024-01-25 11:12:26
tags:fastadmin
---

# 一、常用设置

## 1.1 通用搜索栏设置
```
       // 初始化表格
        table.bootstrapTable({
            url: $.fn.bootstrapTable.defaults.extend.index_url,
            pk: 'id',
            sortName: 'id',
            fixedColumns: true,
            fixedRightNumber: 1,
            //切换列表显示样式开关
            showToggle: false,
            //是否显示右上角的搜索框
            search: false,
            //控制显示列开关
            showColumns:false,
            showExport: false,
            //显示搜索字段框
            searchFormVisible: true,
            //导出开关            
            showExport: true,
            //设置导出  格式
            exportTypes: ['json', 'xml', 'csv', 'txt', 'doc', 'excel'],
         })
```
## 1.2 input 常用设置

data-rule 设置 检查规则 可以自定义验证方法 checksite JS并相关设置
data-source select 可以设置数据源
示例
```
//input设置

<input id="c-site_id" data-rule="checksite" data-source="hj212/pollutionsite/index" data-field="site_name" class="form-control selectpage" name="row[site_id]" type="text" value="">


//js中设置

var Controller = {
        index: function () {
            // 初始化表格参数配置
            Table.api.init({
                ...
            });

            var table = $("#table");

            // 初始化表格
            table.bootstrapTable({
                url: $.fn.bootstrapTable.defaults.extend.index_url,
                pk: 'id',
                sortName: 'id',

                columns: [
                    [
                        {checkbox: true},
                        {field: 'id', title: __('Id'),operate:false},
                  
                        {field: 'operate', title: __('Operate'), table: table, events: Table.api.events.operate, formatter: Table.api.formatter.operate,
                                 buttons:[
                                     {
                                      'name':'bindsite',
                                      'title':function(row){
                                          return '更换站点[ '+row.device_code+']';
                                      },
                                      'icon':'fa fa-pencil',
                                      'text':'更换站点',
                                      'classname': 'btn btn-xs btn-info btn-dialog',
                                      'url':'hj212/device/bindsite/deviceId/{ids}',
                                      'extend': 'data-area=\'["95%","95%"]\''
                                     },
                                     {
                                      'name':'siteInfo',
                                      'title':function(row){
                                          return '站点信息[ '+row.site+']';
                                      },
                                      'text':'站点信息',
                                      'classname': 'btn btn-xs btn-primary btn-dialog',
                                      'url':function(row){
                                          return 'hj212/pollutionsite/siteinfo/site_id/'+row.site_id;
                                       },
                                      'extend': 'data-area=\'["95%","95%"]\''
                                     },
                                 ]}
                    ]
                ],
                exportTypes: [ 'excel'],
            });

            // 为表格绑定事件
            Table.api.bindevent(table);
        },
        add: function () {
            Controller.api.bindevent();
        },
        edit: function () {
            Controller.api.bindevent();
        },
        bindsite:function(){
            Controller.api.bindevent();
        },
        api: {
            bindevent: function () {
	                $.validator.config({
                    rules: {
                        checksite: function (element) {
                            return $.ajax({
                                url: 'hj212/device/checksite',
                                type: 'POST',
                                data: {
                                    site_id: $("#c-site_id").val(),
                                    id:$("#c-id").val()
                                },
                                dataType: 'json'
                            });
                        },
                    }
                });
                Form.api.bindevent($("form[role=form]"));
            }
        }
    };

```
    

# 二、数据库操作

# 三、redis相关操作

# 四、消息队列设置

# 五、命令行开发


