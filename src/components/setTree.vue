<template>
  <div class="set">
    <tree :treeData="treeData"
    :treeSerial="1"
    :defaultChecked="defaultChecked"
    ></tree>
  </div>
</template>

<script lang="ts">
import { Component, Vue, Prop } from 'vue-property-decorator';
import tree from '@/components/tree.vue';

@Component({
    components: { tree },
})
export default class SetTree extends Vue {
@Prop() private treeData!: any;
  @Prop() private triangle!: boolean | undefined;
  @Prop() private checkbox!: boolean | undefined;
  @Prop() private defaultExpanded!: number[] | undefined;
  @Prop() private defaultChecked!: number[] | undefined;

  // method
  public initialize(treeData: any, count: number): void {
    treeData.map((item: any, index: number) => {
      item.layer = count;
      item.triangle = this.triangle;
      item.checkbox = this.checkbox;
      if (this.defaultExpanded as number[] || this.defaultChecked as number[]) {
        // tslint:disable-next-line:max-line-length
        if ((this.defaultExpanded && (this.defaultExpanded as number[]).indexOf(item.id) + 1) || (this.defaultChecked && (this.defaultChecked as number[]).indexOf(item.id) + 1)) {
          item.flag = true;
        }
      }
      if (item.list) {
        this.initialize(item.list, count + 1);
      }
    });
  }
  public check(treeData: any): boolean | undefined {
    let i: number = 0;
    for (const item of treeData) {
      i++;
      if (item.flag) {
        if (item.list) {
          const c = this.check(item.list);
          if (c) {
            item.flag = true;
            return true;
          }
        }
        return true;
      } else if (item.list) {
        const c: any = this.check(item.list);
        if (c) {
          item.flag = true;
          return true;
        }
      }
    }
  }

// 生命周期
  private mounted(): void {
    this.initialize(this.treeData, 1);
    this.check(this.treeData);
    this.treeData.push();
  }
}
</script>

<style scoped>
</style>
