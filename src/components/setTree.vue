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

interface TreeFormat {
  title: string;
  id: number;
  list?: TreeFormat[];
  flag?: boolean;
  check?: number;
  checkbox?: boolean;
  layer?: number;
  triangle?: boolean;
}

@Component({
    components: { tree },
})
export default class SetTree extends Vue {
@Prop() private treeData!: TreeFormat[];
  @Prop() private triangle!: boolean | undefined;
  @Prop() private checkbox!: boolean | undefined;
  @Prop() private defaultExpanded!: number[] | undefined;
  @Prop() private defaultChecked!: number[] | undefined;

  // method
  public initialize(treeData: TreeFormat[], count: number): void {
    treeData.map((item: TreeFormat, index: number) => {
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
  public check(treeData: TreeFormat[]): boolean | undefined {
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
        const c: boolean | undefined = this.check(item.list);
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
