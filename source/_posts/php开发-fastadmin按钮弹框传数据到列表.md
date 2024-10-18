---
title: php开发-fastadmin按钮弹框传数据到列表
tags: fastadmin
categories:
  - PHP开发
abbrlink: 6df754b5
date: 2024-06-28 22:13:59
---
# 一、实现效果
点击按钮 弹出选项列表，选择列表通过table 实现的
难点：
     1.点击弹框url中传值 怎么传到列表请求中，因为table 数据是通过ajax 试下的，就麻烦了 
     2.点击选择按钮的实现 数据的回传 接收处理
     
     
![lena](../pic/dialogbox.png)
# 二、按钮页面 js代码
```js
    $("#select-coupon-btn").click(function () {
                parent.Fast.api.open("wanlshop/street_coupon/select?select_use_type=2", __('选择卡券'), {
                    area: ['800px', '600px'],
                    callback: (data)=> {
                        if(data){
                            $("#coupon-table tbody tr").last().remove();
                            $("#send_gift").val(data.id);
                            if (data.grant == -1) {
                                data.grant = "不限";
                            }
                            $("#coupon-table tbody").last().append("<tr><td>"+data.name+"</td><td>"+data.type+"</td><td>"+data.grant+"</td></tr>");
                        }
                    }
                });
            });

```

# 三、弹框页面

通过Fast.api.query('select_use_type'); 获取 然后加到ajax请求中

弹框页面js代码
```js

    select: function () {
        // 初始化表格参数配置
        Table.api.init({
            extend: {
                index_url: 'wanlshop/street_coupon/select',
            }
        });
        var urlArr = [];
    
        var table = $("#table");
    
        // 多选的 操作
        table.on('check.bs.table uncheck.bs.table check-all.bs.table uncheck-all.bs.table', function (e, row) {
            if (e.type == 'check' || e.type == 'uncheck') {
                row = [row];
            } else {
                urlArr = [];
            }
            $.each(row, function (i, j) {
                if (e.type.indexOf("uncheck") > -1) {
                    var index = urlArr.indexOf(j.id);
                    if (index > -1) {
                        urlArr.splice(index, 1);
                    }
                } else {
                    urlArr.indexOf(j.id) == -1 && urlArr.push(j.id);
                }
            });
        });
    
        // 初始化表格
        table.bootstrapTable({
            url: $.fn.bootstrapTable.defaults.extend.index_url,
            sortName: 'id',
            showToggle: false,
            showExport: false,
    
            //是否显示右上角的搜索框
            search: false,
            //控制显示列开关
            showColumns:false,
            //显示搜索字段框
            searchFormVisible: false,
            showExport: false,
            columns: [
                [
                    {
                        field: 'type',
                        title: __('Type'),
                        searchList: {
                            "reduction": "满减券",
                            "discount": "折扣券",
                            "daijin": "代金券",
                            "stopcar": "停车券",
                            "gift": "礼品券"
                        },
                        formatter: Table.api.formatter.normal
                    },
                    {field: 'name', title: __('Name'), operate: 'LIKE'},
                    {field: 'drawlimit', title: __('Drawlimit')},
                    {field: 'grant', title: __('Grant'), operate: 'LIKE',formatter: Controller.api.formatter.grant},
                    {
                        field: 'operate', title: __('Operate'), events: {
                            'click .btn-chooseone': function (e, value, row, index) {
                                // var multiple = Backend.api.query('multiple');
                                // multiple = multiple == 'true' ? true : false;
                                Fast.api.close({
                                    id: row.id,
                                    name: row.name,
                                    type: row.type_text,
                                    drawlimit: row.drawlimit,
                                    grant: row.grant
                                });
                            },
                        }, formatter: function () {
                            return '<a href="javascript:;" class="btn btn-danger btn-chooseone btn-xs"><i class="fa fa-check"></i> ' + __('Choose') + '</a>';
                        }
                    }
                ]
            ],
            queryParams: function(params){
                var filter = JSON.parse(params.filter);
                var op = JSON.parse(params.op);
                var select_use_type = Fast.api.query('select_use_type');
    
                if (select_use_type != null){
                    filter.use_type= select_use_type;
                    op.use_type = "=";
                }
                params.filter = JSON.stringify(filter);
                params.op = JSON.stringify(op);
                return params;
            }
        });
        // 为表格绑定事件
        Table.api.bindevent(table);
    },
```

后端代码 parent::index() 代码服务一份相应控制器就好了
