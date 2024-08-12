---
title: php开发-fastadmin表格操作列自定义按钮
abbrlink: 5f9fc809
date: 2024-08-08 20:30:54
tags:
---

# 自定义弹框效果按钮

classname字段控制 btn-dialog

```js
     {
        name: 'changerecord',
        text: '动账记录查询',
        icon: 'fa fa-search',
        classname: 'btn btn-info btn-xs btn-dialog',
        url: 'cms/bank/changeRecord'
     },
```

# 需要确认后再处理

visible 字段可根据数据判断 是否显示按钮

```js
         {
            name: 'jy',
            text: '解约',
            icon: 'fa fa-search',
            classname: 'btn btn-info btn-xs btn-danger btn-ajax',
            url: 'cms/bank/unwind',
            visible: function (row) {
                if (row.status == 0) {
                    return true;
                } else {
                    return false;
                }
            },
            confirm: '确定要解约吗?',
            success: function (data, ret) {
                $("a.btn-refresh").trigger("click");
                layer.alert(ret.msg);

                //如果需要阻止成功提示，则必须使用return false;
                //return false;
            },
            error: function (data, ret) {
                console.log(data, ret);
                layer.alert(ret.msg);
                return false;
            }
         }
```    

# 控制字段的显示

formatter字段中处理

```js 
 {
    field: 'status', title: '状态',operate: false, formatter: function (value) {
        if (value == 0) {
            return "已签约";
        } else if (value == 1) {
            return "未签约";
        }
    }
}
```
```js
  {field: 'grant', title: __('Grant'), operate: 'LIKE',formatter: Controller.api.formatter.grant}
```

# 自定义编辑删除按钮

```js
   {
        name: 'edit',
        icon: 'fa fa-pencil',
        title: __('Edit'),
        extend: 'data-toggle="tooltip"',
        classname: 'btn btn-xs btn-success btn-editone',
        visible: function (row) {
            return row.status === 0;
        }
    },
   {
        name: 'del',
        icon: 'fa fa-trash',
        title: __('Del'),
        extend: 'data-toggle="tooltip"',
        classname: 'btn btn-xs btn-danger btn-delone',
        visible: function (row) {
            return row.status === 0;
        }
   }
```

# 自定义提交处理

```js
  add: function () {
            Controller.api.selectcoupon();
            Controller.api.selectusers();
            
            Form.api.bindevent($("form[role=form]"), function(data, ret){
                //如果我们需要在提交表单成功后做跳转，可以在此使用location.href="链接";进行跳转
            }, function(data, ret){
                // 错误处理
                Fast.api.close();
            }, function(success, error){
                //bindevent的第三个参数为提交前的回调
                //如果我们需要在表单提交前做一些数据处理，则可以在此方法处理
                //注意如果我们需要阻止表单，可以在此使用return false;即可
                //如果我们处理完成需要再次提交表单则可以使用submit提交,如下
                if(!$("#user_ids").val()){
                    Toastr.error("请选择发放用户");
                    return false;
                }
                if(!$("#couponid").val()){
                    Toastr.error("请选择发放卡券");
                    return false;
                }
            });

      }
```

# 完整 示例代码
    
    
```html
    columns: [
                    [
                        {checkbox: true},
                        {field: 'id', title: "ID",operate: false},
                        {field: 'account', title: "账号", operate: 'like'},
                        {field: 'school.school_name', title: "学校", operate: 'like'},
                        {field: 'balance', title: "余额/元", operate: false},
                        {
                            field: 'status', title: '状态',operate: false, formatter: function (value) {
                                if (value == 0) {
                                    return "已签约";
                                } else if (value == 1) {
                                    return "未签约";
                                }
                            }
                        },
                        {
                            field: 'operate', title: __('Operate'), table: table,
                            events: Table.api.events.operate,
                            buttons: [
                                {
                                    name: 'changerecord',
                                    text: '动账记录查询',
                                    icon: 'fa fa-search',
                                    classname: 'btn btn-info btn-xs btn-dialog',
                                    url: 'cms/bank/changeRecord'
                                },
                                {
                                    name: 'freezerecord',
                                    text: '冻结/解冻记录',
                                    icon: 'fa fa-search',
                                    classname: 'btn btn-info btn-xs btn-detail btn-dialog',
                                    url: 'cms/bank/freezeRecord',
                                },
                                {
                                    name: 'balance',
                                    text: '查询余额',
                                    icon: 'fa fa-search',
                                    classname: 'btn btn-info btn-xs btn-balance',
                                },
                                {
                                    name: 'qy',
                                    text: '签约',
                                    icon: 'fa fa-search',
                                    classname: 'btn btn-info btn-xs btn-success btn-ajax',
                                    url: 'cms/bank/sign',
                                    visible: function (row) {
                                        if (row.status == 1) {
                                            return true;
                                        } else {
                                            return false;
                                        }
                                    },
                                    success: function (data, ret) {
                                        $("a.btn-refresh").trigger("click");
                                        layer.alert(ret.msg);
                                        //如果需要阻止成功提示，则必须使用return false;
                                        //return false;
                                    },
                                    error: function (data, ret) {
                                        console.log(data, ret);
                                        layer.alert(ret.msg);
                                        return false;
                                    }
                                },
                                {
                                    name: 'jy',
                                    text: '解约',
                                    icon: 'fa fa-search',
                                    classname: 'btn btn-info btn-xs btn-danger btn-ajax',
                                    url: 'cms/bank/unwind',
                                    visible: function (row) {
                                        if (row.status == 0) {
                                            return true;
                                        } else {
                                            return false;
                                        }
                                    },
                                    confirm: '确定要解约吗?',
                                    success: function (data, ret) {
                                        $("a.btn-refresh").trigger("click");
                                        layer.alert(ret.msg);

                                        //如果需要阻止成功提示，则必须使用return false;
                                        //return false;
                                    },
                                    error: function (data, ret) {
                                        console.log(data, ret);
                                        layer.alert(ret.msg);
                                        return false;
                                    }

                                },
                                {
                                    name: 'dj',
                                    text: '冻结',
                                    icon: 'fa fa-search',
                                    classname: 'btn btn-info btn-xs btn-danger btn-ajax',
                                    url: 'cms/bank/frozen',
                                    confirm: '确定要冻结吗?',
                                    success: function (data, ret) {
                                        $("a.btn-refresh").trigger("click");
                                        layer.alert(ret.msg);

                                        //如果需要阻止成功提示，则必须使用return false;
                                        //return false;
                                    },
                                    error: function (data, ret) {
                                        console.log(data, ret);
                                        layer.alert(ret.msg);
                                        return false;
                                    }

                                },
                             {
                                name: 'edit',
                                icon: 'fa fa-pencil',
                                title: __('Edit'),
                                extend: 'data-toggle="tooltip"',
                                classname: 'btn btn-xs btn-success btn-editone',
                                visible: function (row) {
                                    return row.status === 0;
                                }
                            },
                            {
                                name: 'del',
                                icon: 'fa fa-trash',
                                title: __('Del'),
                                extend: 'data-toggle="tooltip"',
                                classname: 'btn btn-xs btn-danger btn-delone',
                                visible: function (row) {
                                    return row.status === 0;
                                }
                            }
                        ], formatter: Table.api.formatter.operate}
                        }

                    ]
                ]
```