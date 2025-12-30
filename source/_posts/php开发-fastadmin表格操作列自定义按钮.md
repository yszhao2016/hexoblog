---
title: php开发-fastadmin表格操作列自定义按钮
abbrlink: 5f9fc809
date: 2024-08-08 20:30:54
tags: fastadmin
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

# 自定义提交处理、自定义js处理

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
    freezerecord: function () {
        // 初始化表格参数配置
        Table.api.init({
            extend: {
                detail_url: '/cms/school/detail',
                index_url: '/admin/cms/school/index',
            }
        });
        var table = $("#table");
        // 初始化表格
        table.bootstrapTable({
            url: 'cms/bank/freezeRecord?ids=' + Fast.api.query('ids'),
            pk: 'id',
            showColumns: false,
            showRefresh: false,
            checkboxHeader: false,
            showToggle: false,
            searchFormVisible: true,
            // commonSearch:false,
            pagination: true,
            pageSize: 5,
            search: false,
            sortName: 'workdate',
            columns: [
                [
                    {
                        field: 'workdate',
                        title: "交易日期",
                        operate: 'RANGE',
                        addclass: 'datetimerange',
                        // formatter: Table.api.formatter.date,
                        // defaultValue: year + '-' + month + '-' + day  + " 00:00:00 - " + now.getFullYear() + '-' + now.getMonth() + '-' + now.getDay()+ ' 23:59:59'
                    },
                    {field: 'actno', title: "监管账号", operate: false},
                    {field: 'djbh', title: "冻结编号", operate: false},
                    {field:'dzbz',title:"类型",formatter: function(value){
                            if(value==0){
                                return "<span style='color: gray;'>解冻</span>";
                            }else if(value==1){
                                return "<span style='color:green'>冻结</span>";
                            }
                        },searchList: {"0":"解冻","1":"冻结"}},
                    {field: 'djje', title: "冻结金额", operate: false},
    
                ]
            ],
    
            responseHandler: function (res) {
                return {'total': res.total, 'rows': res.rows};
            },
    
        });
        // 为表格绑定事件
        Table.api.bindevent(table);
    
    
    }
    changerecord: function () {
        // 保存原始方法
        var originalDaterangepicker = Form.events.daterangepicker;
        // 重写daterangepicker方法
        Form.events.daterangepicker = function(form) {
            if ($(".datetimerange", form).size() > 0) {
                require(['bootstrap-daterangepicker'], function () {
                    var ranges = {};
                    // 特定页面的配置
                    ranges[__('Today')] = [Moment().startOf('day'), Moment().endOf('day')];
                    ranges[__('Yesterday')] = [Moment().subtract(1, 'days').startOf('day'), Moment().subtract(1, 'days').endOf('day')];
                    ranges[__('Last 7 Days')] = [Moment().subtract(6, 'days').startOf('day'), Moment().endOf('day')];
                    ranges['1个月内'] = [Moment().subtract(1, 'month').startOf('day'), Moment().endOf('day')];
                    ranges['3个月内'] = [Moment().subtract(3, 'months').startOf('day'), Moment().endOf('day')];
                    ranges['半年内'] = [Moment().subtract(6, 'months').startOf('day'), Moment().endOf('day')];
                    ranges['1年内'] = [Moment().subtract(1, 'year').startOf('day'), Moment().endOf('day')];
    
                    var options = {
                        timePicker: false,
                        autoUpdateInput: false,
                        timePickerSeconds: true,
                        timePicker24Hour: true,
                        autoApply: true,
                        locale: {
                            format: 'YYYY-MM-DD HH:mm:ss',
                            customRangeLabel: __("Custom Range"),
                            applyLabel: __("Apply"),
                            cancelLabel: __("Clear"),
                        },
                        ranges: ranges,
                    };
    
                    // 保留原始的回调处理逻辑
                    var origincallback = function (start, end) {
                        $(this.element).val(start.format(this.locale.format) + " - " + end.format(this.locale.format));
                        $(this.element).trigger('blur');
                    };
                    $(".datetimerange", form).each(function () {
                        var callback = typeof $(this).data('callback') == 'function' ? $(this).data('callback') : origincallback;
                        $(this).on('apply.daterangepicker', function (ev, picker) {
                            callback.call(picker, picker.startDate, picker.endDate);
                        });
                        $(this).on('cancel.daterangepicker', function (ev, picker) {
                            $(this).val('').trigger('blur');
                        });
                        $(this).daterangepicker($.extend(true, options, $(this).data()), callback);
                    });
                });
            }
        };
        // 初始化表格参数配置
        Table.api.init({
            extend: {
                detail_url: '/cms/school/detail',
                index_url: '/admin/cms/school/index',
            }
        });
        var table = $("#table");
        // 初始化表格
        table.bootstrapTable({
            url: 'cms/bank/changeRecord?ids=' + Fast.api.query('ids'),
            pk: 'id',
            showColumns: false,
            showRefresh: false,
            checkboxHeader: false,
            showToggle: false,
            searchFormVisible: true,
            // commonSearch:false,
            pagination: true,
            pageSize: 5,
            search: false,
            sortName: 'workdate',
            columns: [
                [
                    {
                        field: 'workdate',
                        title: "交易日期",
                        operate: 'RANGE',
                        addclass: 'datetimerange',
                        // formatter: Table.api.formatter.datetime
                    },
                    {field: 'actno', title: "监管账号", operate: false},
                    {field: 'jdflag', title: "借贷标志", operate: false},
                    {field: 'amt', title: "交易金额", operate: false},
                    {field: 'balance', title: "账户余额", operate: false},
                    // {field: 'dfactno', title:"对方客户账号",operate:false},
                    {field: 'dfactname', title: "对方账户名称", operate: false},
                    {field: 'dfjgmc', title: "对方金融机构名称", operate: false},
                    // {field: 'dfjgdm', title:"对方金融机构代码",operate:false},
                    // {field: 'summary', title:"摘要",operate:false},
                    // {field: 'brno', title:"交易营业机构",operate:false},
                ]
            ],
    
            responseHandler: function (res) {
                return {'total': res.total, 'rows': res.rows};
            },
    
        });
        // 为表格绑定事件
        Table.api.bindevent(table);
        
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