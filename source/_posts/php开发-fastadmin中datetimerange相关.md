---
title: php开发-fastadmin中datetimerange相关
abbrlink: '6739304'
date: 2025-12-17 12:57:40
tags:
---

# datetimerange自定义快捷选项
![datetimerange.png](../pic/datetimerange.png)


```javascript
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


        },
```

# datetimerange自定义快捷选项删除快捷选择

```javascript
 {field: 'workdate',title: "交易日期",operate: 'RANGE',addclass: 'datetimerange',
    extend:'data-ranges=""',// formatter: Table.api.formatter.datetime},
```
   