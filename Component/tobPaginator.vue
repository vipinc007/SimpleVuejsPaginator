<template>
<div>
  <div class="form-inline">
    <select v-show="numberOfPages()>0" class="form-control" id="sel1" v-model="crecPerPage" v-on:change="changeRecordPerPage($event)">
    <option v-for="k in numberOfRecordsInEachPage" :key="k">{{k}}</option>
    
  </select>
  <input v-show="cdataList.length>0" type="text" @input="dosearch" class="form-control col-lg-11"  placeholder="Search" />
  </div>
  <slot></slot>
<nav aria-label="...">
  <!-- {{first_disabled}}|{{prev_disabled}}|{{next_disabled}}|{{last_disabled}} -->

  <ul v-show="numberOfPages()>0" class="pagination pagination-sm">
    <li class="page-item">
      <a class="page-link" style="background-color:#A2F2F3">

          showing {{itemStartPositionForLabel()}}-{{itemEndPositionForLabel()}} of <strong>{{cdataList.length}}</strong>
      </a>
    </li>
    <li class="page-item " v-bind:class="{ 'disabled': first_disabled}">
      <a class="page-link"  tabindex="-1" style="cursor: pointer;" v-on:click="gotopage(1)">First</a>
    </li>
    <li class="page-item " v-bind:class="{ 'disabled': prev_disabled}">
      <a class="page-link"  tabindex="-1" style="cursor: pointer;" v-on:click="prev()">Prev</a>
    </li>
    <li class="page-item" v-for="p in calculatePageRanges()" :key="p" v-bind:class="{ 'active': cPage==p}">
      
      <a class="page-link" v-show="p!='...'" style="cursor: pointer;" v-on:click="gotopage(p)">{{p}}</a>
      <h2 v-show="p=='...'"> . . . </h2>
      
      </li>
    <li class="page-item " v-bind:class="{ 'disabled': next_disabled}">
      <a class="page-link"   style="cursor: pointer;" v-on:click="next()">Next</a>
    </li>
    <li class="page-item " v-bind:class="{ 'disabled': last_disabled}">
      <a class="page-link"  style="cursor: pointer;" v-on:click="gotopage(numberOfPages())">Last</a>
    </li>
    
  </ul>

    

</nav>
</div>
</template>

<script>
export default {
  name: "tobPaginator",
  // props: ['dataList', 'recordsPerPage','curPageNumber','methodToCallBack'],
  props: { 
    dataList: {
      type: Object,
      required: true
    },
    recordsPerPage: {
      type: Number,
      required: true
    },
     curPageNumber: {
      type: Number,
      required: true
    },
    methodToCallBack: {
      type: Function,
      required: true
    },
  },
  data() {
    return { 
        cPage : this.curPageNumber,
        crecPerPage  : this.recordsPerPage,
        cdataList  : this.dataList,
        cdataListCopy  : this.dataList,
        cdataListFiltered  : this.dataList,
        first_disabled : true,
        prev_disabled : true,
        last_disabled : false,
        next_disabled : false,
        numberOfRecordsInEachPage : [5,10,15,25,50,100]
      };
  },

  mounted: function () {
      this.$nextTick(function () {
       
        this.sendPaginatedList();
      });
    },
    
  created()
  {
    this.prepareButtons();
    if(this.dataList === undefined)
    {
      this.dataList = [];
      this.cdataList = [];
      this.cdataListCopy = [];
      this.cdataListFiltered = [];
    }
  },
  methods: {
    dosearch({ type, target })
    {

    if(target.value!="")
    {
     var searcheditems= [];
     var lucky = this.cdataListCopy.filter(function(item) {

          let myKeys = Object.keys(item);
          myKeys.forEach(key => {

            if(item[key]!=null)
            {
                     if(!Array.isArray(item[key]))
                     {
                      if(item[key].toLowerCase().includes(target.value.toLowerCase()))
                      searcheditems.push(item);
                     }
                     else
                     {
                       
                     }
            }
           });
      });

      this.cdataListFiltered = searcheditems;
      this.cdataList = searcheditems;
    }
    else
    this.cdataList = this.cdataListCopy;

    if(this.cPage>this.numberOfPages())
        this.cPage = this.numberOfPages();
    if(this.numberOfPages()==0)
        this.cPage = 1;
      this.sendPaginatedList();

    },
    changeRecordPerPage: function changeItem(event) {
      if(this.cPage>this.numberOfPages())
        this.cPage = this.numberOfPages();
      this.sendPaginatedList();
    },
    sendPaginatedList()
    {
      this.prepareButtons();
      this.methodToCallBack(this.paginated(), this.cdataListFiltered);
    },
    gotopage(pnumber)
    {
      this.cPage = parseInt(pnumber);
      
      this.sendPaginatedList();
    },
    next()
    {
      this.cPage++;
      this.sendPaginatedList();
    },
    calculatePageRanges()
    {
          var current = this.cPage,
            last = this.numberOfPages(),
            delta = 2,
            left = current - delta,
            right = current + delta + 1,
            range = [],
            rangeWithDots = [],
            l;

        for (let i = 1; i <= last; i++) {
            if (i == 1 || i == last || i >= left && i < right) {
                range.push(i);
            }
        }

        for (let i of range) {
            if (l) {
                if (i - l === 2) {
                    rangeWithDots.push(l + 1);
                } else if (i - l !== 1) {
                    rangeWithDots.push('...');
                }
            }
            rangeWithDots.push(i);
            l = i;
        }

        return rangeWithDots;
    },
    numberOfPages()
    {
      if(this.cdataList == undefined || this.cdataList==null)
      return 0;
      var remindr = (this.cdataList.length % this.crecPerPage);
      
      var totalpages= parseInt((this.cdataList.length / this.crecPerPage))  + (remindr>=1?1:0);
      return totalpages;
    },
    prepareButtons()
    {
      this.first_disabled = false;
      this.prev_disabled = false;
      this.last_disabled = false;
      this.next_disabled = false;

      if(this.cPage==1)
      {
        this.first_disabled = true;
        this.prev_disabled = true;
      }

      if(parseInt(this.cPage)==parseInt(this.numberOfPages()))
      {
        this.last_disabled = true;
        this.next_disabled = true;
      }
    },
    prev()
    {
      

      this.cPage--;
      
      
      this.sendPaginatedList();
    },
    itemStartPosition()
    {
      return ((this.cPage-1)*this.crecPerPage);
    },
    itemEndPosition()
    {
      return this.crecPerPage*this.cPage;
    },
    itemStartPositionForLabel()
    {
      return ((this.cPage-1)*this.crecPerPage)+1;
    },
    itemEndPositionForLabel()
    {
      var endcount= this.crecPerPage*this.cPage;
      if(endcount>this.cdataList.length)
        return this.cdataList.length;
      return endcount;
    },
    paginated()
    {
      var items = this.cdataList.slice(this.itemStartPosition(),this.itemEndPosition());
      return items;
    },
    updateItems(items)
    {
      this.cdataList = items;
      this.cdataListCopy = items;
      this.sendPaginatedList();
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped></style>
