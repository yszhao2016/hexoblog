---
title: php开发-fastadmin键值组件(Fieldlist)
abbrlink: c70fd524
date: 2024-08-08 19:56:55
tags: fastadmin
---

# fieldlist中有select

selectpicker貌似不兼容的
模版语法 art-template
edit 页面select默认选中值 [先执行php代码 最后js取渲染模版的]


art-template中使用if
```js
<%if(…){%>  html  <%}else{%> html  <%}%>
```

edit页面 代码示例
```html
   <script type="text/html" id="testtpl">
        <dd class="form-inline">
            <input type="hidden" name="row[<%=name%>][<%=index%>][id]" class="form-control" value="<%=row['id']%>" size="2">

            <select   data-id="<%=index%>" class="couponname form-control"  name="row[<%=name%>][<%=index%>][coupon_id]" class="form-control" style="width: 111px">
                <option value="">请选择</option>

                <?php
                                            foreach($coupon as $vo){
                                               echo '<option name="'.$vo['name'].'" value="'.$vo['id'].'"     <% if (row["coupon_id"]&&row["coupon_id"].indexOf("'.$vo["id"].'")>-1) { %> selected <% } %>     >'.$vo['name'].'</option>';
                }

                ?>
                <option value="-1">谢谢参与</option>
            </select>
            <input type="text"   id="num<%=index%>"  name="row[<%=name%>][<%=index%>][num]" readonly class="form-control" value="<%=row['num']%>" size="2">
            <input type="text" name="row[<%=name%>][<%=index%>][scale]" class="form-control" value="<%=row['scale']%>" size="1">%
            <input type="text" name="<%=name%>[<%=index%>][icon]" id="c-downloadurl-<%=index%>" class="form-control" value="<%=row.icon%>" style="width:140px;"/>
            <div class="btn-group">
                <button type="button" id="plupload-downloadurl-<%=index%>" class="btn btn-danger plupload" data-input-id="c-downloadurl-<%=index%>" data-mimetype="*" data-multiple="false"><i class="fa fa-upload"></i></button>
            </div>
        </dd>
    </script>
```