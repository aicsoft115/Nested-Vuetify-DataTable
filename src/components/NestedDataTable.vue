<template>
  <v-data-table
    v-model="selected"
    :headers="headers"
    :items="treats"
    :pagination.sync="pagination"
    expand
    select-all
    item-key="category"
    class="elevation-1"
    hide-actions
  >
    <template v-slot:items="props">
      <tr>
        <td>
          <v-checkbox primary hide-details v-model="props.selected" @change="onChangeSelect(props.item, 0)" />
        </td>
        <td @click="props.expanded = !props.expanded" class="text-xs-left" colspan="6">
          {{props.item.category}}
        </td>
      </tr>
    </template>

    <template v-slot:expand="props">
      <v-data-table
        v-model="selected"
        :headers="headers"
        :items="props.item.food"
        :pagination.sync="pagination"
        expand
        select-all
        item-key="name"
        class="elevation-1"
        hide-actions
        hide-headers
      >
        <template v-slot:items="props">
          <tr>
            <td style="width: 8rem">
              <v-checkbox primary hide-details v-model="props.selected" @change="onChangeSelect(props.item, 1)" />
            </td>
            <td @click="props.expanded = !props.expanded" class="text-xs-left" colspan="6">{{props.item.name}}</td>
          </tr>
        </template>

        <template v-slot:expand="props">
          <v-data-table
            v-model="selected"
            :headers="headers"
            :items="props.item.materials"
            :pagination.sync="pagination"
            select-all
            item-key="name"
            class="elevation-1"
            hide-actions
            hide-headers
          >
            <template v-slot:items="props">
              <tr>
                <td style="width: 10rem">
                  <v-checkbox primary hide-details v-model="props.selected" @change="onChangeSelect(props.item, 2)" />
                </td>
                <td @click="props.expanded = !props.expanded" class="text-xs-left">{{props.item.name}}</td>
                <td @click="props.expanded = !props.expanded" class="text-xs-left">{{props.item.calories}}</td>
                <td @click="props.expanded = !props.expanded" class="text-xs-left">{{props.item.fat}}</td>
                <td @click="props.expanded = !props.expanded" class="text-xs-left">{{props.item.carbs}}</td>
                <td @click="props.expanded = !props.expanded" class="text-xs-left">{{props.item.protein}}</td>
                <td @click="props.expanded = !props.expanded" class="text-xs-left">{{props.item.iron}}</td>
              </tr>
            </template>
          </v-data-table>

        </template>
      </v-data-table>

    </template>
  </v-data-table>
</template>

<script>
  export default {
    name: 'NestedDataTable',
    props: {
      msg: String
    },
    data() {
      return {
        pagination: {
          sortBy: 'name'
        },
        selected: [],
        triggerWatcher: true,
        headers: [
          {
            text: 'Dessert (100g serving)',
            align: 'left',
            sortable: false,
            value: 'name'
          },
          { text: 'Calories', value: 'calories' },
          { text: 'Fat (g)', value: 'fat' },
          { text: 'Carbs (g)', value: 'carbs' },
          { text: 'Protein (g)', value: 'protein' },
          { text: 'Iron (%)', value: 'iron' }
        ],
        treats: [
          {
            category: 'Desserts',
            food: [
              {
                name: 'Frozen Yogurt',
                calories: 159,
                fat: 6.0,
                carbs: 24,
                protein: 4.0,
                iron: '1%'
              },
              {
                name: 'Ice cream sandwich',
                calories: 237,
                fat: 9.0,
                carbs: 37,
                protein: 4.3,
                iron: '1%',
                materials: [
                  {
                    name: 'material 3',
                    calories: 237,
                    fat: 9.0,
                    carbs: 37,
                    protein: 4.3,
                    iron: '1%'
                  },
                  {
                    name: 'material 4',
                    calories: 237,
                    fat: 9.0,
                    carbs: 37,
                    protein: 4.3,
                    iron: '1%'
                  }
                ]
              },
              {
                name: 'Cupcake',
                calories: 305,
                fat: 3.7,
                carbs: 67,
                protein: 4.3,
                iron: '8%',
                materials: [
                  {
                    name: 'material 5',
                    calories: 237,
                    fat: 9.0,
                    carbs: 37,
                    protein: 4.3,
                    iron: '1%'
                  },
                  {
                    name: 'material 6',
                    calories: 237,
                    fat: 9.0,
                    carbs: 37,
                    protein: 4.3,
                    iron: '1%'
                  }
                ]
              }
            ]
          },
          {
            category: 'Entries'
          }
        ]
      }
    },
    methods: {
      onChangeSelect(item, level) {
        this.triggerWatcher = false
        const select = this.selected.includes(item)
        this.changeSelectDown(item, level, select)
        this.changeSelectUp(item, level)
        this.triggerWatcher = true
      },
      changeSelectDown(item, level, select) {
        let selected = this.selected
        const vm = this

        if (level === 0) {
          if (item.food) {
            item.food.forEach(function (food) {
              if (select) {
                if (!selected.includes(food)) selected.push(food)
              } else {
                if (selected.includes(food)) {
                  const index = selected.indexOf(food)
                  selected.splice(index, 1)
                }
              }
              vm.changeSelectDown(food, level+1, select)
            })
          }
        } else if (level === 1) {
          if (item.materials) {
            item.materials.forEach(function (material) {
              if (select) {
                if (!selected.includes(material)) selected.push(material)
              } else {
                if (selected.includes(material)) {
                  const index = selected.indexOf(material)
                  selected.splice(index, 1)
                }
              }
              vm.changeSelectDown(material, level+1, select)
            })
          }
        }

        this.selected = selected
      },
      changeSelectUp(item, level) {
        let selected = this.selected
        const vm = this

        if (level === 1) {
          this.treats.forEach(function (treat) {
            if (treat.food) {
              if (treat.food.includes(item)) { // parent
                let select = true
                treat.food.forEach(function (food) {
                  if (!selected.includes(food)) {
                    select = false
                    return
                  }
                })
                if (select) {
                  if (!selected.includes(treat)) selected.push(treat)
                } else {
                  if (selected.includes(treat)) {
                    const index = selected.indexOf(treat)
                    selected.splice(index, 1)
                  }
                }
                return
              }
            }
          })
        } else if (level === 2) {
          this.treats.forEach(function (treat) {
            if (treat.food) {
              treat.food.forEach(function (food) {
                if (food.materials) {
                  if (food.materials.includes(item)) { // parent
                    let select = true
                    food.materials.forEach(function (material) {
                      if (!selected.includes(material)) {
                        select = false
                        return
                      }
                    })
                    if (select) {
                      if (!selected.includes(food)) selected.push(food)
                    } else {
                      if (selected.includes(food)) {
                        const index = selected.indexOf(food)
                        selected.splice(index, 1)
                      }
                    }
                    vm.changeSelectUp(food, level-1)
                    return
                  }
                }
              })
            }
          })
        }
        this.selected = selected
      }
    },
    watch: {
      selected(newSel, oldSel) {
        if (this.triggerWatcher) {
          this.triggerWatcher = false
          const vm = this
          this.selected.forEach(function (item) {
            if (item.food) {
              vm.onChangeSelect(item, 0)
            } else {
              if (item.materials) {
                vm.onChangeSelect(item, 1)
              } else {
                vm.onChangeSelect(item, 2)
              }
            }
          })
          this.triggerWatcher = true
        }
      }
    }
  }
</script>

<style scoped>

</style>
