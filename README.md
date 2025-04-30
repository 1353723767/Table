# 自写表格组件

1、支持表格的表头、列固定；

2、支持表格的虚拟滚动加载；

3、支持表格的行选中；

4、支持获取选中行数据；

5、支持定位到表格指定行；


/*使用示例*/

```
//vue
<!--dataKey=生成表格内容数据循环时用到的key，用来减少渲染次数，提高性能，如果不传则默认用数组下标-->
<!--maxRow=懒加载数据一次性最大加载数量，默认不传是一次性加载10条数据渲染表格展示-->
<!--checkbox=是否需要展示勾选列，传对象，show=true则展示勾选列，fixed=true则勾选列固定在左侧-->
<Table
    ref="tableRef"
    class="max-h600"
    dataKey="dataId"
    :maxRow="10"
    :checkbox="{
        show: true, //是否显示表格的勾选项列
		fixed: true, //勾选项列是否需要固定在左侧
        class: '' //需要给勾选列额外添加的class类
    }"
>
    <!--表头内容插槽-->
    <template #header>
        <!--如果需要设置表格左侧固定列，则需要给th设置class=table-fixed-box fixed-left；固定左侧列用fixed-left，固定右侧列用fixed-right-->
        <th class="table-fixed-box fixed-left"><div>姓名</div></th>
        <th><div>年龄</div></th>
        <th><div>手机号</div></th>
        <!--如果需要设置表格右侧固定列，则需要给th设置class=table-fixed-box fixed-right；固定左侧列用fixed-left，固定右侧列用fixed-right-->
        <th class="table-fixed-box fixed-right"><div>操作</div></th>
    </template>
    <!--表格内容插槽，可以获取：row=表格行数据，index=表格行数据下标-->
    <template #body="{ row, index }">
        <!--如果需要设置表格左侧固定列，则需要给td设置class=table-fixed-box fixed-left；固定左侧列用fixed-left，固定右侧列用fixed-right-->
        <td class="table-fixed-box fixed-left"><div>{{ row.name }}</div></td>
        <td><div>{{ row.age }}</div></td>
        <td><el-input v-model="row.phone"/></td>
        <!--如果需要设置表格右侧固定列，则需要给td设置class=table-fixed-box fixed-right；固定左侧列用fixed-left，固定右侧列用fixed-right-->
        <td class="table-fixed-box fixed-right">
            <i class="icon_delete" title="删除"></i>
        </td>
    </template>
</Table>
```
```
//js
<script setup lang="ts">
import {reactive, ref} from 'vue';
import Table from '@/components/Table.vue';

const TableData = reactive({
	data: [{ //生成表格内容数据
		dataId: '1',
		name: '张三', //name对应表头列数据的key=name的姓名列数据
		age: 23, //age对应表头列数据的key=age的年龄列数据
		phone: '12345678910' //phone对应表头列数据的key=phone的手机号列数据
	}, {
		dataId: '2',
		name: '李四',
		age: 24,
		phone: '12345678910'
	}]
});
const tableRef = ref(); //表格组件ref
onMounted(() => {
	// 表格数据初始化，因为组件里面没有做watch监听，所以需要调用表格组件的更新视图方法，否则表格数据不会更新
	tableRef.value.updateTableView(TableData.data, 'init');
});
//获取表格选中的数据集合
const getTableCheckData = () => {
	//调用getCheckData方法，如果不传参数则返回选中的那条数据的整个对象数组集合，如果传了参数则返回指定参数的数组集合
	
	let data = tableRef.value.getCheckData();  //如果勾选了第一条数据，且调用getCheckData没传参数，则这里获取到的data=[{dataId:'1', name: '张三', age: 23, phone: '12345678910'}]
	
	let data = tableRef.value.getCheckData('dataId');  //如果勾选了第一条数据，且调用getCheckData传了参数，则这里获取到的data=['1']
};

//表格数据变更时，调用表格组件的更新视图方法，否则表格数据不会更新
const updateTableViewFn = () => {
	TableData.data.push({
		dataId: '3',
		name: '王五',
		age: 25,
		phone: '12345678910'
	});
	//表格数据更新后需要手动调用一下表格组件的更新视图方法，否则表格数据不会更新，不传init时渲染当前已经渲染的条数(如滚动加载了30条数据，则当前还是渲染30条)，且保留当前滚动条所在位置
	tableRef.value.updateTableView(TableData.data);
	
	//如果是需要初始化只渲染前10条表格数据，同时滚动条初始化到顶部，则需要传init参数
	tableRef.value.updateTableView(TableData.data, 'init');
};

//跳转定位到表格指定行位置
const scrollToRowFn = () => {
	//第一个参数为要跳转到哪一行
	//第二个参数为是否跳转到指定行且需要定位在指定行的子节点位置，如果传了，则跳转到子节点，如果没有传，则跳转到整行，一般在表格有横向滚动条时需要用到
	tableRef.value.scrollToRow(30, '.children-class');
};
< /script>
```
