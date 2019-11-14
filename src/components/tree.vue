<template>
  <div class="tree">
    <div v-for="(item,index) in treeData" :key="index">
      <div class="box" :style="{'padding-left': item.layer*10+'px'}" @click.stop="doClick(index)">
        <span v-if="item.list&&item.triangle" :class="item.flag?'b':'c'" :style="{'left': item.layer*10-10+'px'}"></span>
        <div v-if="item.checkbox" class="checkBox" @click.stop="doSelect(index)">
          <div :class="item.check?'ba':''">{{item.check === 2?'-':'✓'}}</div>
        </div>
        <span>{{item.title}}</span>
      </div>
      <div v-if="item.list">
        <tree v-if="item.flag"
        :treeData="item.list"
        :treeSerial="index"
        :defaultChecked="defaultChecked"
        @getSelect="setSelect"></tree>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue, Prop } from 'vue-property-decorator';
import tree from '@/components/tree.vue';


@Component({
  name: 'tree',
})
export default class Tree extends Vue {
@Prop() private treeData!: any;
  @Prop() private treeSerial!: number;
  @Prop() private defaultChecked!: number[] | undefined;

  // method
  public doClick(i: number): void {
    this.treeData.map((item: any, index: number) => {
      if (index === i) {
        item.flag = !item.flag;
        if (item.list) {
          item.list.push();
        }
      }
    });
  }
  public setSelect(serial: any, val: number) {
    let checkOne: boolean = true;
    let checkTwo: boolean = true;
    let checkThree: boolean = true;
    this.treeData.map((item: any, index: number) => {
      if (index === serial) {
        item.check = val;
      }
      item.check ? checkOne = false : checkTwo = false;
      item.check === 2 ? checkThree = false : item.check = item.check;
    });
    this.$emit('getSelect', this.treeSerial, 2);
    if (checkOne) {
      this.$emit('getSelect', this.treeSerial, 0);
    }
    if (checkTwo && checkThree) {
      this.$emit('getSelect', this.treeSerial, 1);
    }
    this.treeData.push();
  }
  public doSelect(i: number) {
    let value: number;
    let checkOne: boolean = true;
    let checkTwo: boolean = false;
    this.treeData[i].check === 1 ? value = 0 : value = 1;
    this.treeData.map((item: any, index: number) => {
      if (index === i) {
        item.check = value;
        this.doCount(item, value);
      }
      item.check ? checkOne = false : checkTwo = true;
    });
    this.$emit('getSelect', this.treeSerial, 2);
    if (checkOne) {
      this.$emit('getSelect', this.treeSerial, 0);
    }
    if (!checkTwo) {
      this.$emit('getSelect', this.treeSerial, 1);
    }
    this.treeData.push();
  }
  public doCount(item: any, value: number) {
    if (item.list) {
      item.list.map((idx: any, index: number) => {
        idx.check = value;
        if (idx.list) {
          this.doCount(idx, value);
        }
      });
      item.list.push();
    }
  }
  public doChecked(treeData: any, defaultChecked: number[]) {
    // console.log(defaultChecked)
    treeData.map((item: any, index: number) => {
      if (defaultChecked.indexOf(item.id) + 1) {
        const i: number = index;
        this.doSelect(i);
        if (item.list && defaultChecked[0]) {
          this.doChecked(item.list, defaultChecked);
        }
      } else if (item.list && defaultChecked[0]) {
        this.doChecked(item.list, defaultChecked);
      }
    });
  }
// 生命周期
  private mounted(): void {
    if (this.defaultChecked && this.defaultChecked[0]) {
      this.doChecked(this.treeData, this.defaultChecked);
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
