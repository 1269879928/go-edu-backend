<style lang='less'>
  .table-basic-vue {}
</style>
<template>
  <div class="table-basic-vue frame-page h-panel">
    <div class="h-panel-bar"><span class="h-panel-title">课程分类</span></div>
    <div class="h-panel-body">
       <p>
         <Button class="h-btn h-btn-primary" icon="h-icon-plus" @click="create()">添加</Button>
       </p>
        <Table :loading="loading" :datas="datas">
          <TableItem :width="60" title="序号">
            <template slot-scope="{index}">{{index+1}} </template>
          </TableItem>
          <TableItem  prop="name" title="分类名称"></TableItem>
          <TableItem  :width="200" prop="sort" title="排序"></TableItem>
          <TableItem  title="状态">
            <template slot-scope="{ data }">
              <span class="h-tag h-tag-green" v-if="data.status==1">显示</span>
              <span class="h-tag h-tag-red" v-else>隐藏</span>
            </template>
          </TableItem>
          <TableItem :width="170" prop="created_at" title="创建日期" :format="dateFormat"></TableItem>
          <TableItem title="操作" align="center" :width="130">
          <template slot-scope="{ data }">
            <Poptip content="确定要执行该操作吗？" @confirm="remove(datas, data)">
              <button class="h-btn h-btn-s h-btn-red">{{data.status === 0? '显示' : '隐藏'}}</button>
            </Poptip>
            <button class="h-btn h-btn-s h-btn-primary" @click="edit(data)">编辑</button>
          </template>
        </TableItem>
        </Table>
        <p></p>
        <Pagination v-if="pagination.total>0"  align="right" v-model="pagination" @change="changePage" />
    </div>
  </div>
</template>
<script>
import manba from 'manba';
export default {
  data() {
    return {
      keyword: '',
      pagination: {
        page: 1,
        size: 20,
        total: 0
      },
      type: 'China',
      datas: [],
      counts: {},
      loading: false
    };
  },
  mounted() {
    this.getData();
  },
  methods: {
    changePage() {
      this.getData(true);
    },
    dateFormat(value) {
      if (!value) {
        return null;
      } else {
        return manba(value).format('YYYY/MM/DD HH:mm:ss');
      }
    },
    getData(reload) {
      if (reload) {
        this.pagination.page = 1;
      }
      this.loading = true;
      R.CourseCategoryies.index(this.pagination).then(resp => {
        if (resp.code === 0) {
          let data = resp.data;
          this.datas = data.list;
          this.pagination.total = data.total;
        } else {
          HeyUI.$Message.error(resp.msg);
        }
        this.loading = false;
      });
    },
    create() {
      this.$router.push({ name: 'CourseCategoriesCreate' });
    },
    remove(data, item) {
      let id = item.id;
      let status = item.status ? 0 : 1;
      R.CourseCategoryies.delete({ id, status }).then(resp => {
        if (resp.code === 0) {
          // data.indexOf(item)
          item.status = status;
          HeyUI.$Message.success('更新成功！');
          return;
        }
        HeyUI.$Message.error(resp.msg);
      });
    },
    edit(item) {
      this.$router.push({ name: 'CourseCategoriesEdit', params: { id: item.id } });
    }
  },
  computed: {

  }
};
</script>
