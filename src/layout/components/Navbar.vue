<template>
  <div class="navbar">
    <hamburger :is-active="sidebar.opened" class="hamburger-container" @toggleClick="toggleSideBar" />

    <breadcrumb v-if="device === 'desktop'" class="breadcrumb-container" />
    <div v-else class="path-name">{{ $route.meta.title }}</div>

    <div class="right-menu">
      <el-select
        :value="teachingTaskId"
        placeholder="教学任务"
        :style="teachingTaskSelectStyle"
        class="filter-item"
        :size="device === 'desktop' ? 'small' : 'mini'"
        @change="(teachingTaskId) => {setTeachingTaskId(teachingTaskId)}"
      >
        <el-option
          v-for="item in teachingTask"
          :key="item.id"
          :label="item.showName"
          :value="item.id"
        />
      </el-select>
      <div class="avatar" @click.stop="uploadHeadImg">
        <img :src="userInfo.teaPhoto ? userInfo.teaPhoto : teaPhoto" alt="">
      </div>
      <input type="file" accept="image/*" class="hiddenInput" @change="handleFile">
      <el-dropdown trigger="click" class="dropdown">
        <span class="el-dropdown-link">
          <i class="el-icon-caret-bottom" />
        </span>
        <el-dropdown-menu slot="dropdown">
          <router-link to="/personal">
            <el-dropdown-item>
              个人信息
            </el-dropdown-item>
          </router-link>
          <el-dropdown-item divided @click.native="logout">
            <span style="display:block;">
              <svg-icon icon-class="tuichu" />
              退出
            </span>
          </el-dropdown-item>
        </el-dropdown-menu>
      </el-dropdown>
    </div>
    <!-- <span>{{ userInfo }}</span> -->
  </div>
</template>
<script>
import { mapGetters, mapActions, mapMutations } from 'vuex'
import Breadcrumb from '@/components/Breadcrumb'
import Hamburger from '@/components/Hamburger'
import SvgIcon from '@/components/SvgIcon'
import defaultAvatar from '@/assets/images/user.gif'
export default {
  components: {
    Breadcrumb,
    Hamburger,
    SvgIcon
  },
  data() {
    return {
      teaPhoto: defaultAvatar
    }
  },
  computed: {
    ...mapGetters([
      'sidebar',
      'userInfo',
      'teachingTask',
      'teachingTaskId'
    ]),
    device() {
      return this.$store.state.app.device
    },
    teachingTaskSelectStyle() {
      return { width: this.device === 'desktop' ? '280px' : '135px' }
    }
  },
  methods: {
    ...mapActions({
      'uploadAvatar': 'user/uploadAvatar'
    }),
    ...mapMutations({
      'setTeachingTaskId': 'user/SET_TEACHING_TASK_ID'
    }),
    // 打开图片上传
    uploadHeadImg: function() {
      this.$el.querySelector('.hiddenInput').click()
    },
    // 将头像显示
    handleFile: function(e) {
      const $target = e.target || e.srcElement
      const file = $target.files[0]
      // 上传头像
      const params = new FormData()
      params.append('photo', file, file.name)
      this.uploadAvatar(params)
    },
    toggleSideBar() {
      this.$store.dispatch('app/toggleSideBar')
    },
    async logout() {
      await this.$store.dispatch('user/logout')
      this.$router.push('/login')
    }
  }
}
</script>

<style lang="scss" scoped>
.navbar {
  // display: flex;
  height: 50px;
  overflow: hidden;
  position: relative;
  background: #fff;
  box-shadow: 0 1px 4px rgba(0,21,41,.08);

  .path-name {
    float: left;
    line-height: 50px;
  }
  .hamburger-container {
    line-height: 46px;
    height: 100%;
    float: left;
    cursor: pointer;
    transition: background .3s;
    -webkit-tap-highlight-color:transparent;

    &:hover {
      background: rgba(0, 0, 0, .025)
    }
  }

  .breadcrumb-container {
    float: left;
  }

  .right-menu {
    float: right;
    height: 100%;
    line-height: 50px;
    display: flex;
    .el-select {
      margin-right: 10px;
    }
    &:focus {
      outline: none;
    }
    .hiddenInput {
      display: none;
    }
    .avatar {
      margin-top: 5px;
      margin-right: 5px;
      cursor: pointer;
      img {
        width: 40px;
        height: 40px;
        border-radius: 5px;
      }
    }
    .dropdown {
      margin-right: 20px;
      .el-dropdown-link {
        cursor: pointer
      }
    }
  }
}
</style>
