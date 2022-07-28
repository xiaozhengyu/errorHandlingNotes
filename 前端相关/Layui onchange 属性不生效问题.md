# Layui onchange 属性不生效问题

---

### 代码1：

```html
<div class="layui-form">
    <div class="layui-form-item ">
        <label class="layui-form-label" for="citySelector">城市：</label>
        <div class="layui-input-inline ">
            <select id="citySelector" name="city" onchange="selectCity()">
                <option value="1">北京</option>
                <option value="2">上海</option>
                <option value="3">广州</option>
            </select>
        </div>
    </div>
</div>

<script>

    function selectCity() {
        alert("onchange 属性生效");
    }
</script>
```

上面代码的本意是通过 onchange 属性为选择框设置事件，然而实际的效果是 onchange 没有生效，这是因为使用了 layui-form 样式，只要移除这个样式 onchange 属性马上就生效了。



### 代码2：

如果必须使用 layui-form 样式，但是又想为 select 设置事件，可以使用 layui 的 lay-filer 属性。

```js
<div class="layui-form"><!--layui-form 使得 onchange 属性失效-->
    <div class="layui-form-item ">
        <label class="layui-form-label" for="citySelector">城市：</label>
        <div class="layui-input-inline ">
            <select id="citySelector" name="city" lay-filter="selectCityFilter">
                <option value="1">北京</option>
                <option value="2">上海</option>
                <option value="3">广州</option>
            </select>
        </div>
    </div>
</div>

<script>

    function selectCity() {
        alert("onchange 属性生效");
    }

    layui.use(['jquery', 'form'], function () {
        var $ = layui.jquery;
        var form = layui.form;

        form.on("select(selectCityFilter)", function (data) {
            // 可以利用 data 获取选项的信息
            selectCity();
        });
    });
</script>
```