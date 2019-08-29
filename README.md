# SimpleVuejsPaginator

Hello, This is a simple vue.js pagination component


## Usage :
*	Download this component.
*	Put is inside anywhere in your project folder.
*	Import it in your component
	```
	import tobPaginator from '@/<path>/tobPaginator.vue';
	
	export default {
	name: "LogActivity",
	components :{
		tobPaginator 
	},
	```
*	Usage
	```
	<component :is="tobPaginator_component" :key="Log" ref="tobPaginator_component" 
            :dataList="Log" :recordsPerPage="10" :curPageNumber="1" :methodToCallBack="getPaginatedLog">
            <div class="table-responsive" style="height: 30rem">
                
                <table id="logactivity_data" class="table table-bordered table-hover table-outline table-vcenter text-nowrap card-table">
                  <thead>
                    <tr>
                      
                      <th width='15%'>Name</th>
                      <th width='60%'>Address</th>
                      
                      <th width='15%'>Age</th>
                      <th width='10%'>Gender</th>
                    </tr>
                  </thead>
                  <tbody>
                    <tr v-for="item in paginatedLogData">
                      
                      <td>
                        {{item.Name}} 
                        
                      </td>
                      <td>
                        {{item.Address}} 
                       
                      </td>
                      <td>
                        {{item.Age}}
                        
                      </td>
                      <td>
                        {{item.Gender}}
                        
                      </td>
                  
                    </tr>
                </table>
                
                
           
                <!-- <span v-html="pg.pagelinks()"></span> -->
              </div>
            </component>
	```

	```
	methods: {
    
    getPaginatedLog(items, itemsFiltered)
    {
      this.paginatedLogData = items;
    }
	}
	```
