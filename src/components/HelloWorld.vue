<template>
  <div class="hello">
    <h2>Statistics by Route</h2>
    <table  align="left" cellpadding="2"  border="0">
      <tr>
        <td>DELIVERY_TERMINAL</td>
        <td>TRIP_NUMBER</td>
        <td>PICKUP_TERMINAL</td>
        <td>BILL_NUMBER </td>
        <td> ORIGNAME</td>
        <td>ORIGPROV</td>
        <td>ORIGPC</td>
        <td>DESTNAME</td>
        <td>DESTPROV</td>
        <td>DESTPC</td>
        <td>CURRENT_ZONE </td>
        <td>CURRENT_STATUS</td>
        <td>PICK_UP_BY</td>
        <td>DELIVER_BY</td>
        <td>SERVICE_LEVEL</td>
        <td>PICK_UP_DRIVER</td>
        <td>ROLLUP_PIECES</td>
        <td>ROLLUP_WEIGHT</td>
        <td>ROLLUP_PALLETS</td>
        <td>PICKUP_ZONE</td>
        <td>DELIVERY_ZONE</td>
        <td>LANE</td>
        <td>NAME</td>
        <td>RPT</td>
      </tr>
      <tr>
        <td><hr></td>
        <td><hr></td>
        <td><hr></td>
        <td><hr></td>
        <td><hr></td>
        <td><hr></td>
        <td><hr></td>
        <td><hr></td>
        <td><hr></td>
        <td><hr></td>
        <td><hr></td>
        <td><hr></td>
        <td><hr></td>
        <td><hr></td>
        <td><hr></td>
        <td><hr></td>
        <td><hr></td>
        <td><hr></td>
        <td><hr></td>
        <td><hr></td>
        <td><hr></td>
        <td><hr></td>
        <td><hr></td>
        <td><hr></td>
      </tr>

      <tr  v-for="t in trips"  >
        <td>{{t.DELIVERY_TERMINAL}}</td>
        <td>{{t.TRIP_NUMBER}}</td>
        <td>{{t.PICKUP_TERMINAL}}</td>
        <td>{{ t.BILL_NUMBER }}</td>
        <td> {{ t.ORIGNAME }}</td>
        <td>{{t.ORIGPROV}}</td>
        <td> {{t.ORIGPC}}</td>
        <td>{{t.DESTNAME}}</td>
        <td>{{t.DESTPROV}}</td>
        <td>{{t.DESTPC}}</td>
        <td >{{ t.CURRENT_ZONE}} </td>
        <td >{{ t.CURRENT_STATUS }}</td>
        <td>{{ formatDate(t.PICK_UP_BY)}}</td>
        <td>{{formatDate(t.DELIVER_BY)}}</td>
        <td>{{ t.SERVICE_LEVEL }}</td>
        <td>{{t.PICK_UP_DRIVER}}</td>
        <td>{{t.ROLLUP_PIECES}}</td>
        <td>{{t.ROLLUP_WEIGHT}}</td>
        <td> {{t.ROLLUP_PALLETS}}</td>
        <td>{{t.PICKUP_ZONE}}</td>
        <td >{{ t.DELIVERY_ZONE }}</td>
        <td>{{t.LANE}}</td>
        <td>{{t.NAME}}</td>
        <td>{{t.RPT}}</td>
      </tr>
    </table>
<hr>

      <ul  v-for="m in builtTree" >
        {{m.OBW}}
        {{m.OBPlt}}
        {{m.OBPcs}}
        {{m.OnDocW}}
        {{m.OnDocPlt}}
        {{m.OnDocPcs}}
        <li> {{m.term}}</li>
      </ul>
    <br/>
    <hr>
    <!--<div>-->
      <!--<tree-view :data="builtTree" :options="{maxDepth: 3}"></tree-view>-->
    <!--</div>-->
  </div>
</template>

<script>
  import axios from 'axios'

  export default {
    name: 'HelloWorld',
    props: ['terminal', 'start', 'end', 'label', 'nodes'],
    data () {
      return {
        msg: 'Hi',
        trips: [],
        builtTree: [{ term: '',
          OBW: 0,
          OBPlt: 0,
          OBPcs: 0,
          OnDocW: 0,
          OnDocPlt: 0,
          OnDocPcs: 0,
          OB: {
            OBW1: 0,
            OBPlt1: 0,
            OBPcs1: 0,
            trip: [
              {
                tripNo: 0,
                OBW2: 0,
                OBPlt2: 0,
                OBPcs2: 0
              }
            ]
          },
          OD: {
            OnDocW1: 0,
            OnDocPlt1: 0,
            OnDocPcs1: 0,
            trip: [
              {
                tripNo: 0,
                OnDocW2: 0,
                OnDocPlt2: 0,
                OnDocPcs2: 0
              }
            ]
          }
        }],
        abc: 'Howdy'
      }
    },
    watch: {
      '$route' (to, from) {
      }
    },
    mounted: function () {
      this.fetchData()
      this.builtTree = this.computed(this.trips)
    },
    methods: {
      onFinish () {
        this.fetchData()
        console.log(this.builtTree.length)
      },
      formatDate (text) {
        if (text != null) {
          text = text.substr(0, 19)
        }
        return text
      },
      formatHours (hr) {
        if (hr != null) {
          var a = hr.toFixed(2)
        }
        return a
      },
      computed (tree) {
        // var myTree = tree
        var obWeight = 0
        var obPlts = 0
        var obPcs = 0
        var odWgt = 0
        var odPlts = 0
        var odPcs = 0
        var dt = ''
        // var OBW1 = 0
        // var OBPlt1 = 0
        // var OBPcs1 = 0
        // var OBW2 = 0
        // var OBPlt2 = 0
        // var OBPcs2 = 0
        // var OnDocW2 = 0
        // var OnDocPlt2 = 0
        // var OnDocPcs2 = 0
        // var trip = 0
        // var myMapT = [{ term: '', OBW: 0, OBPlt: 0, OBPcs: 0 }]
        var myMap = [{
          'OBW': 0,
          'OBPlt': 0,
          'OBPcs': 0,
          'OnDocW': 0,
          'OnDocPlt': 0,
          'OnDocPcs': 0,
          'term': {
            'term2': '',
            'OB': {
              'OBW1': 0,
              'OBPlt1': 0,
              'OBPcs1': 0
            },
            'OD': {
              'OnDocW1': 0,
              'OnDocPlt1': 0,
              'OnDocPcs1': 0
            }
          }
        }]
        // ,
          // OB: {
          //   OBW1: 0,
          //   OBPlt1: 0,
          //   OBPcs1: 0,
          //   trip: [
          //     {
          //       tripNo: 0,
          //       OBW2: 0,
          //       OBPlt2: 0,
          //       OBPcs2: 0
          //     }
          //   ]
          // },
          // OD: {
          //   OnDocW1: 0,
          //   OnDocPlt1: 0,
          //   OnDocPcs1: 0,
          //   trip: [
          //     {
          //       tripNo: 0,
          //       OnDocW2: 0,
          //       OnDocPlt2: 0,
          //       OnDocPcs2: 0
          //     }
          //   ]
          // }

        for (var s of tree) {
           // myMap.indexOf(s.DELIVERY_TERMINAL).data
          if (s.RPT === 'OutBillsOnDoc') {
            obWeight = obWeight + s.ROLLUP_WEIGHT
            obPlts = obPlts + s.ROLLUP_PALLETS
            obPcs = obPcs + s.ROLLUP_PIECES
          } else {
            odWgt = odWgt + s.ROLLUP_WEIGHT
            odPlts = odPlts + s.ROLLUP_PALLETS
            odPcs = odPcs + s.ROLLUP_PIECES
          }
          dt = s.DELIVERY_TERMINAL
          // myMap.term = dt
        }
        // myMap.push(obWeight, obPlts, obPcs, odWgt, odPlts, odPcs)
        myMap.OBW = obWeight
        myMap.OBPlt = obPlts
        myMap.OBPcs = obPcs
        myMap.OnDocW = odWgt
        myMap.OnDocPlt = odPlts
        myMap.OnDocPcs = odPcs
        myMap.term = {}
        myMap.term.OB = {}
        myMap.term.OD = {}
        myMap.term.term2 = dt
        // console.log('Term2' + myMap.term.term2)
        for (var t of tree) {
          dt = t.DELIVERY_TERMINAL
          if (myMap.term.term2.indexOf(t.DELIVERY_TERMINAL) === -1) {
            myMap.term[t.DELIVERY_TERMINAL] = t.DELIVERY_TERMINAL
            // myMap.term[t.DELIVERY_TERMINAL].OB = {}
            myMap.term.OB.OBW1 = t.ROLLUP_WEIGHT
            // console.log('Term2: ' + myMap.term[t.DELIVERY_TERMINAL])
          } else {
            // myMap.term.term2.OB.OBW1 = myMap.term.term2(t.DELIVERY_TERMINAL).OB.OBW1 + t.ROLLUP_WEIGHT
            console.log(myMap.term.OB.OBW1 + t.ROLLUP_WEIGHT)
          }
        }
          // else {
          //   myMap.set(myMap[myMap.indexOf(t.DELIVERY_TERMINAL)].OBW + t.ROLLUP_WEIGHT)
          // }
          // console.log(myMap[myMap.indexOf(t.DELIVERY_TERMINAL)])
          // console.log(myMap[myMap.indexOf(t.ROLLUP_WEIGHT)])
          // myMap.set([myMap.indexOf(t.DELIVERY_TERMINAL)].OBW + t.ROLLUP_WEIGHT)
          // console.log(myMap[myMap.indexOf(t.ROLLUP_WEIGHT)].OBW)
          // myMap[myMap.indexOf(t.DELIVERY_TERMINAL)].OBW = myMap[myMap.indexOf(t.DELIVERY_TERMINAL)].OBW + t.ROLLUP_WEIGHT

          // else {
          //   if (t.RPT === 'OutBillsOnDoc') {
          //     myMap.set(([myMap.indexOf(t.DELIVERY_TERMINAL)].OBW + t.ROLLUP_WEIGHT), ([myMap.indexOf(t.DELIVERY_TERMINAL)].OBW + t.ROLLUP_WEIGHT), ([myMap.indexOf(t.DELIVERY_TERMINAL)].OBPcs + t.ROLLUP_PIECES))
          //     if (myMap.indexOf(t.DELIVERY_TERMINAL).trip.indexOf(t.TRIP_NUMBER) === -1) {
          //       myMap.set([myMap.indexOf(t.DELIVERY_TERMINAL)].trip.push([t.TRIP_NUMBER, t.ROLLUP_WEIGHT, t.ROLLUP_PALLETS, t.ROLLUP_PIECES]))
          //     } else {
          //       myMap.set(([myMap.indexOf(t.DELIVERY_TERMINAL)].trip.indexOf(t.TRIP_NUMBER).OBW1 + t.ROLLUP_WEIGHT), ([myMap.indexOf(t.DELIVERY_TERMINAL)].trip.indexOf(t.TRIP_NUMBER).OBPlt1 + t.ROLLUP_PALLETS), ([myMap.indexOf(t.DELIVERY_TERMINAL)].trip.indexOf(t.TRIP_NUMBER).OBPcs1 + t.ROLLUP_PIECES))
          //     }
          //   } else {
          //     myMap.set(([myMap.indexOf(t.DELIVERY_TERMINAL)].OnDocW + t.ROLLUP_WEIGHT), ([myMap.indexOf(t.DELIVERY_TERMINAL)].OnDocPlt + t.ROLLUP_PALLETS), ([myMap.indexOf(t.DELIVERY_TERMINAL)].OnDocPcs + t.ROLLUP_PIECES))
          //     if (myMap.indexOf(t.DELIVERY_TERMINAL).trip.indexOf(t.TRIP_NUMBER) === -1) {
          //       myMap.set([myMap.indexOf(t.DELIVERY_TERMINAL)].trip.push([t.TRIP_NUMBER, t.ROLLUP_WEIGHT, t.ROLLUP_PALLETS, t.ROLLUP_PIECES]))
          //     } else {
          //       myMap.set(([myMap.indexOf(t.DELIVERY_TERMINAL)].trip.indexOf(t.TRIP_NUMBER).OnDocW1 + t.ROLLUP_WEIGHT), ([myMap.indexOf(t.DELIVERY_TERMINAL)].trip.indexOf(t.TRIP_NUMBER).OnDocPlt1 + t.ROLLUP_PALLETS), ([myMap.indexOf(t.DELIVERY_TERMINAL)].trip.indexOf(t.TRIP_NUMBER).OnDocPcs1 + t.ROLLUP_PIECES))
          //     }
          //   }
          // }
        console.log('OB Weight ' + obWeight)
        console.log('OB Pallets ' + obPlts)
        console.log('OB Pcs ' + obPcs)
        console.log('On Doc Weight ' + odWgt)
        console.log('On Doc Plts ' + odPlts)
        console.log('On Doc Pcs ' + odPcs)
        console.log(myMap)
        return myMap
      },
      fetchData: function () {
        axios.get('http://localhost:1337/docStats')
          .then(response => {
            this.trips = response.data
            this.builtTree = this.computed(this.trips)
          })
          .catch(e => {
            this.errors.push(e)
          })
      }

    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}

.tree-view-item {
  font-family: monospace;
  font-size: 14px;
  margin-left: 18px;
}

.tree-view-wrapper {
  overflow: auto;
}

/* Find the first nested node and override the indentation */
.tree-view-item-root > .tree-view-item-leaf > .tree-view-item {
  margin-left: 0;
}

/* Root node should not be indented */
.tree-view-item-root {
  margin-left: 0;
}
</style>
