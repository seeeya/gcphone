<template>
  <div class="home" v-bind:style="{background: 'url(' + backgroundURL +')'}">
    <InfoBare />
    <span class="warningMess" v-if="messages.length >= warningMessageCount">
      <div class="warningMess_icon"><i class="fa fa-warning"></i></div>
      <span class="warningMess_content">
        <span class="warningMess_title">{{ IntlString('PHONE_WARNING_MESSAGE') }}</span><br>
        <span class="warningMess_mess">{{messages.length}} / {{warningMessageCount}} {{IntlString('PHONE_WARNING_MESSAGE_MESS')}}</span>
      </span>
    </span>
    <div class='home_buttons'>
      <button 
          v-for="(but, key) of AppsHome" 
          v-bind:key="but.name" 
          v-bind:class="{ select: key === currentSelect}"
          v-bind:style="{backgroundImage: 'url(' + but.icons +')'}"
          @click="openApp(but)"
         >
          {{but.intlName}}
          <span class="puce" v-if="but.puce !== undefined && but.puce !== 0">{{but.puce}}</span>
      </button>
      <div class="btn_menu_ctn">
        <button 
          class="btn_menu"
          :class="{ select: AppsHome.length === currentSelect}"
          v-bind:style="{backgroundImage: 'url(' + '/html/static/img/icons_app/menu.png' +')'}"
          @click="openApp({routeName: 'menu'})"
          >
        </button>
      </div>
    </div> 
  </div>
</template>

<script>
import { mapGetters, mapActions } from 'vuex'
import InfoBare from './InfoBare'

export default {
  components: {
    InfoBare
  },
  data () {
    return {
      currentSelect: 0
    }
  },
  computed: {
    ...mapGetters(['IntlString', 'useMouse', 'nbMessagesUnread', 'backgroundURL', 'messages', 'AppsHome', 'warningMessageCount'])
  },
  methods: {
    ...mapActions(['closePhone', 'setMessages']),
    onLeft () {
      this.currentSelect = (this.currentSelect + this.AppsHome.length) % (this.AppsHome.length + 1)
    },
    onRight () {
      this.currentSelect = (this.currentSelect + 1) % (this.AppsHome.length + 1)
    },
    onUp () {
      this.currentSelect = Math.max(this.currentSelect - 4, 0)
    },
    onDown () {
      this.currentSelect = Math.min(this.currentSelect + 4, this.AppsHome.length)
    },
    openApp (app) {
      this.$router.push({ name: app.routeName })
    },
    onEnter () {
      this.openApp(this.AppsHome[this.currentSelect] || {routeName: 'menu'})
    },
    onBack () {
      this.closePhone()
    }
  },
  created () {
    if (!this.useMouse) {
      this.$bus.$on('keyUpArrowLeft', this.onLeft)
      this.$bus.$on('keyUpArrowRight', this.onRight)
      this.$bus.$on('keyUpArrowDown', this.onDown)
      this.$bus.$on('keyUpArrowUp', this.onUp)
      this.$bus.$on('keyUpEnter', this.onEnter)
    } else {
      this.currentSelect = -1
    }
    this.$bus.$on('keyUpBackspace', this.onBack)
  },
  beforeDestroy () {
    this.$bus.$off('keyUpArrowLeft', this.onLeft)
    this.$bus.$off('keyUpArrowRight', this.onRight)
    this.$bus.$off('keyUpArrowDown', this.onDown)
    this.$bus.$off('keyUpArrowUp', this.onUp)
    this.$bus.$off('keyUpEnter', this.onEnter)
    this.$bus.$off('keyUpBackspace', this.onBack)
  }
}
</script>

<style scoped="true">
.home{
  background-size: cover !important;
  background-position: center !important;
  position: relative;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  display: flex;
  align-content: center;
  justify-content: center;
  color: gray;
}
.warningMess{
  background-color: white;
  position: absolute;
  left: 12px;
  right: 12px;
  top: 34px;
  min-height: 64px;
  display: flex;
  padding: 12px;
  border-radius: 4px;
  box-shadow: 0 2px 2px 0 rgba(0,0,0,.14), 0 3px 1px -2px rgba(0,0,0,.2), 0 1px 5px 0 rgba(0,0,0,.12);
}
.warningMess .warningMess_icon{
  display: flex;
  width: 16%;
  align-items: center;
  justify-content: center;
  font-size: 28px;
  height: 42px;
  width: 42px;
  border-radius: 50%;
}
.warningMess .warningMess_icon .fa {
  text-align: center;
  color: #F94B42;
}
.warningMess .warningMess_content{
  padding-left: 12px;
}
.warningMess_title {
  font-size: 20px;
}
.warningMess_mess {
  font-size: 16px;
}

.home_buttons{
  display: flex;
  padding: 0 16px; 
  width: 100%;
  bottom:1px;
  position: absolute;
  align-items: flex-end;
  flex-flow: row;
  flex-wrap: wrap;
  margin-bottom: 0px;
  justify-content: space-between;
  transition: all 0.5s ease-in-out;
}
button{
  position: relative;
  margin: 0px;
  border: none;
  width: 80px;
  height: 110px;
  color: white;
  background-size: 64px 64px;
  background-position: center 6px;
  background-repeat: no-repeat;
  background-color: transparent;
  font-size: 14px;
  padding-top: 72px;
  font-weight: 700;
  text-shadow: -1px 0 0 rgba(0,0,0, 0.8), 
             1px 0 0 rgba(0,0,0, 0.8),
             0 -1px 0 rgba(0,0,0, 0.8),
             0 1px 0 rgba(0,0,0, 0.8);
  text-align: center;
}


button .puce{
  position: absolute;
  display: block;
  background-color: #1450b8;
  font-size: 14px;
  width: 28px;
  height: 28px;
  line-height: 28px;
  text-align: center;
  border-radius: 50%;
  border: 1px solid white;
  bottom: 32px;
  right: 12px;
}
button.select, button:hover{
  background-color: rgba(0,179,255, 0.7);
  border-radius: 12px;
}


.btn_menu_ctn{
  width: 100%;
  display: flex;
  height: 80px;
  justify-content: center;
  align-content: center;
}
.btn_menu {
  height: 50px;
}
.btn_menu.select {
     background-image: url('data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAASwAAAEsCAYAAAB5fY51AAABNWlDQ1BBZG9iZSBSR0IgKDE5OTgpAAAokZWPv0rDUBSHvxtFxaFWCOLgcCdRUGzVwYxJW4ogWKtDkq1JQ5XSJNxc//QhHN06uLj7BE6OgoPiE/gGilMHhwjBqfSbvvPjcDg/MCp23WkYZRjEWrWbjnQ9X86/MscMAHTCLLVbrSOAOIkj/iPg5wMB8LZt150G07EYpkoDY2C3G2UhiArQv9apBjECzKCfahCPgKnO2jUQz0Cpl/s7UApy/wRKyvV8EN+A2XM9H4wFwAxyXwNMHd1ogFqSDtVF71zLqmVZ0u4mQSRPh5mOBpk8jMNEpYnq6KgL5P8BsJwvtpuO3Kha1sHmlL0n4nq+zO3rBAGIlZciKwgv1dWfCmNv8lzcGK3C8QPMjots/xbut2DprsjWq1DegafRL8KzT/57sqVFAAAACXBIWXMAAAsTAAALEwEAmpwYAAAFwWlUWHRYTUw6Y29tLmFkb2JlLnhtcAAAAAAAPD94cGFja2V0IGJlZ2luPSLvu78iIGlkPSJXNU0wTXBDZWhpSHpyZVN6TlRjemtjOWQiPz4gPHg6eG1wbWV0YSB4bWxuczp4PSJhZG9iZTpuczptZXRhLyIgeDp4bXB0az0iQWRvYmUgWE1QIENvcmUgNS42LWMxNDUgNzkuMTYzNDk5LCAyMDE4LzA4LzEzLTE2OjQwOjIyICAgICAgICAiPiA8cmRmOlJERiB4bWxuczpyZGY9Imh0dHA6Ly93d3cudzMub3JnLzE5OTkvMDIvMjItcmRmLXN5bnRheC1ucyMiPiA8cmRmOkRlc2NyaXB0aW9uIHJkZjphYm91dD0iIiB4bWxuczp4bXA9Imh0dHA6Ly9ucy5hZG9iZS5jb20veGFwLzEuMC8iIHhtbG5zOnhtcE1NPSJodHRwOi8vbnMuYWRvYmUuY29tL3hhcC8xLjAvbW0vIiB4bWxuczpzdFJlZj0iaHR0cDovL25zLmFkb2JlLmNvbS94YXAvMS4wL3NUeXBlL1Jlc291cmNlUmVmIyIgeG1sbnM6c3RFdnQ9Imh0dHA6Ly9ucy5hZG9iZS5jb20veGFwLzEuMC9zVHlwZS9SZXNvdXJjZUV2ZW50IyIgeG1sbnM6ZGM9Imh0dHA6Ly9wdXJsLm9yZy9kYy9lbGVtZW50cy8xLjEvIiB4bWxuczpwaG90b3Nob3A9Imh0dHA6Ly9ucy5hZG9iZS5jb20vcGhvdG9zaG9wLzEuMC8iIHhtcDpDcmVhdG9yVG9vbD0iQWRvYmUgUGhvdG9zaG9wIENTNiAoV2luZG93cykiIHhtcDpDcmVhdGVEYXRlPSIyMDE5LTA0LTI1VDE4OjM3OjMzKzAzOjAwIiB4bXA6TW9kaWZ5RGF0ZT0iMjAxOS0wNC0yNVQxODo1MDoyMSswMzowMCIgeG1wOk1ldGFkYXRhRGF0ZT0iMjAxOS0wNC0yNVQxODo1MDoyMSswMzowMCIgeG1wTU06SW5zdGFuY2VJRD0ieG1wLmlpZDpkZWM5ZDM2NS1kMWU0LTg5NGEtYTE4My1mYmIyZmRjZjUwNzkiIHhtcE1NOkRvY3VtZW50SUQ9InhtcC5kaWQ6NzEwMzk1M0U1RTgyMTFFOUIyNTlGMkQ5NUFCM0Q0NkEiIHhtcE1NOk9yaWdpbmFsRG9jdW1lbnRJRD0ieG1wLmRpZDo3MTAzOTUzRTVFODIxMUU5QjI1OUYyRDk1QUIzRDQ2QSIgZGM6Zm9ybWF0PSJpbWFnZS9wbmciIHBob3Rvc2hvcDpDb2xvck1vZGU9IjMiPiA8eG1wTU06RGVyaXZlZEZyb20gc3RSZWY6aW5zdGFuY2VJRD0ieG1wLmlpZDo3MTAzOTUzQjVFODIxMUU5QjI1OUYyRDk1QUIzRDQ2QSIgc3RSZWY6ZG9jdW1lbnRJRD0ieG1wLmRpZDo3MTAzOTUzQzVFODIxMUU5QjI1OUYyRDk1QUIzRDQ2QSIvPiA8eG1wTU06SGlzdG9yeT4gPHJkZjpTZXE+IDxyZGY6bGkgc3RFdnQ6YWN0aW9uPSJzYXZlZCIgc3RFdnQ6aW5zdGFuY2VJRD0ieG1wLmlpZDpkZWM5ZDM2NS1kMWU0LTg5NGEtYTE4My1mYmIyZmRjZjUwNzkiIHN0RXZ0OndoZW49IjIwMTktMDQtMjVUMTg6NTA6MjErMDM6MDAiIHN0RXZ0OnNvZnR3YXJlQWdlbnQ9IkFkb2JlIFBob3Rvc2hvcCBDQyAyMDE5IChXaW5kb3dzKSIgc3RFdnQ6Y2hhbmdlZD0iLyIvPiA8L3JkZjpTZXE+IDwveG1wTU06SGlzdG9yeT4gPC9yZGY6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') !important;
}
</style>
