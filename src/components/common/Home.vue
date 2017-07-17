<template>
    <div class="wrapper">
        <v-head></v-head>
        <v-sidebar></v-sidebar>
        <div class="content">
          <!-- <transition name="move" mode="out-in"><router-view></router-view></transition> -->
          <el-col :span="20" class="content-wrapper">
            <el-tabs v-model="editableTabsValue" type="card" closable @tab-remove="removeTab" @tab-click='tabToggle'>
              <el-tab-pane
                v-for="(item, index) in editableTabs"
                :label="item.title" :name="item.name" :key="item.name"
              >
              </el-tab-pane>
              <keep-alive>
                   <component :is='currentView'></component>
              </keep-alive>
             </el-tabs>
          </el-col>
        </div>
    </div>
</template>

<script>
    import vHead from './Header.vue';
    import vSidebar from './Sidebar.vue';
    import Readme from '../page/Readme.vue';
    import VueTable from '../page/VueTable.vue';
    import BaseTable from '../page/BaseTable.vue';
    import DragList from '../page/DragList.vue'
    export default {
        data () {
          	return {
    	        
          	};
        },
        computed: {
	        editableTabs() {
	          	return this.$store.state.editableTabs
	        },
	        editableTabsValue() {
	          	return this.$store.state.editableTabsValue
	        },
	        currentView() {
	          	return this.$store.state.currentView
	        }
	    },
        components:{
            'vHead' : vHead,
            'vSidebar' : vSidebar,
            'readme' : Readme,
            'vuetable' : VueTable, 
            'basetable' : BaseTable,
            'drag' : DragList
        },
        methods: {
    	    removeTab(targetIndex) {
    	    	var vm = this;
    	        var activeIndex = vm.$store.state.editableTabsValue;
    	        if (activeIndex === targetIndex) {
    	          	vm.editableTabs.forEach(function(tab,index){
    	          		if (tab.name === targetIndex) {
    	          			vm.$store.dispatch('removeTabs', index);
    		              	var nextTab = vm.editableTabs[index + 1] || vm.editableTabs[index - 1];
    		              	if (nextTab) {
    		                	activeIndex = nextTab.name;
    		                	vm.$store.dispatch('changeCurrentView', nextTab.path);
    		                	vm.$store.dispatch('changeTabactiveindex', activeIndex);
    		              	}
    		            }
    	          	})
    	        }else{
    	        	var closedIndex;
    	        	vm.editableTabs.forEach(function(tab,index){
    	          		if (tab.name === targetIndex) {
    	          			closedIndex = index;
    		              	vm.$store.dispatch('removeTabs', index);
    		            }
    	          	})
    	        	
    	        	vm.editableTabs.forEach(function(tab,index){
    	        		if(index >= closedIndex){
    	        			tab.name = parseInt(tab.name) - 1 + "";
    	        		}
    	          	})
    	        	if(activeIndex >=　 closedIndex){
    	        		vm.$store.dispatch('changeTabactiveindex', activeIndex - 1);
    	        	}
    	        }
    	    },
    	    tabToggle(tab){
    	    	var vm = this;
    	    	vm.editableTabs.forEach(function(item,index){
    	    		if(item.title == tab.label){
    	    			vm.$store.dispatch('changeTabactiveindex', index);
    	    			vm.$store.dispatch('changeCurrentView', item.path);
    	    		}
    	    	})
    	    }
        },
        created () {
            console.log(this.router);
        }
    }
</script>
