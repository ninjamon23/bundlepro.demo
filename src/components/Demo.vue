<template>
  <div>
    <div class="columns" v-if="showFolder">
      <div class="column" style="padding: 2rem; text-align: left">
        <h4>Left Side</h4>
        <hr />
        <Draggable
          :defaultFolded="true"
          class="tree8"
          :treeData="inputData"
          virtualization="virtualization"
          style="height: 600px; overflow: auto"
          triggerClass="drag-trigger"
          :edgeScroll="true"
          :gap="6"
          ref="leftTree"
          @dropChange="dropChange"
        >
          <template v-slot="{ node, tree }">
            <!-- {{ node }} -->
            <!-- <button class="drag-trigger" style="margin-right: 0.5em">
              <i class="fas fa-mouse-pointer"></i>
            </button> -->
            <!-- <b @click="tree.toggleFold(node)"
              >{{ node.$folded ? "+" : "-" }}&nbsp;</b
            > -->
            <!-- <input
              type="checkbox"
              v-model="node.$checked"
              @change="tree.updateChecked(node)"
            /> -->
            &nbsp;
            <!-- <span>{{ node.text }}</span> -->
            <span
              class="icon-text drag-trigger"
              @click="tree.toggleFold(node)"
              v-if="node.type === 'folder'"
            >
              <span class="icon" v-if="node.type === 'folder'">
                <i class="fas fa-folder" v-if="node.$folded"></i>
                <i class="fas fa-folder-open" v-if="!node.$folded"></i>
              </span>
              <span @click="treeSelect(node.id, node)">{{ node.text }}</span>
              <!-- <span @click="addLeftNode(node, tree)" style="padding-left: 1rem"
                >[ + ]</span
              > -->
              <p class="buttons" style="padding-left: 2rem">
                <button
                  class="button is-small"
                  @click="addLeftNode(node, tree)"
                  title="Add Folder"
                >
                  <span class="icon is-small">
                    <i class="fas fa-plus"></i>
                  </span>
                  <!-- <span>New Folder</span> -->
                </button>
                <button
                  class="button is-small"
                  @click="editLeftNode(node, tree)"
                  title="Edit Folder"
                >
                  <span class="icon is-small">
                    <i class="fas fa-edit"></i>
                  </span>
                  <!-- <span>Edit Folder</span> -->
                </button>
                <button
                  class="button is-small"
                  @click="deleteLeftNode(node, tree)"
                  title="Delete Folder"
                >
                  <span class="icon is-small">
                    <i class="fas fa-trash"></i>
                  </span>
                  <!-- <span>Delete Folder</span> -->
                </button>
              </p>
            </span>
          </template>
        </Draggable>
      </div>
      <div
        class="column"
        style="padding: 2rem; text-align: left"
        
      >
        <h4>Right Side</h4>
        <hr />
        <Draggable
          :defaultFolded="true"
          class="tree8"
          :treeData="rightData"
          virtualization="virtualization"
          style="height: 600px; overflow: auto"
          triggerClass="drag-trigger"
          :edgeScroll="true"
          :gap="6"
          :ondragend="dropChangeRight"
          v-if="rightData"
        >
          <template v-slot="{ node, tree }">
            <!-- {{ node }} -->
            <!-- <button class="drag-trigger" style="margin-right: 0.5em">
              <i class="fas fa-mouse-pointer"></i>
            </button> -->
            <!-- <b @click="tree.toggleFold(node)"
              >{{ node.$folded ? "+" : "-" }}&nbsp;</b
            > -->
            <!-- <input
              type="checkbox"
              v-model="node.$checked"
              @change="tree.updateChecked(node)"
            /> -->
            &nbsp;
            <!-- <span>{{ node.text }}</span> -->
            <span class="icon-text drag-trigger" @click="tree.toggleFold(node)">
              <span class="icon" v-if="node.type === 'folder'">
                <i class="fas fa-folder" v-if="node.$folded"></i>
                <i class="fas fa-folder-open" v-if="!node.$folded"></i>
              </span>
              <span class="icon" v-else>
                <i class="fas fa-file"></i>
              </span>
              <span>{{ node.text }}</span>
              <p class="buttons" style="padding-left: 2rem">
                <button
                  class="button is-small"
                  @click="addRightNode(node, tree)"
                  title="Add Document"
                >
                  <span class="icon is-small">
                    <i class="fas fa-plus"></i>
                  </span>
                  <!-- <span>New Folder</span> -->
                </button>
                <button
                  class="button is-small"
                  @click="editRightNode(node, tree)"
                  title="Edit Document"
                >
                  <span class="icon is-small">
                    <i class="fas fa-edit"></i>
                  </span>
                  <!-- <span>Edit Folder</span> -->
                </button>
                <button
                  class="button is-small"
                  @click="deleteRightNode(node, tree)"
                  title="Delete Document"
                >
                  <span class="icon is-small">
                    <i class="fas fa-trash"></i>
                  </span>
                  <!-- <span>Delete Folder</span> -->
                </button>
                <button
                  class="button is-small"
                  @click="cutRightNode(node, tree)"
                  title="Cut Document"
                  v-if="!cutNodeVal"
                >
                  <span class="icon is-small">
                    <i class="fas fa-cut"></i>
                  </span>
                </button>
                <button
                  class="button is-small"
                  @click="pasteRightNode(node, tree)"
                  title="Paste Document"
                  v-if="cutNodeVal"
                >
                  <span class="icon is-small">
                    <i class="fas fa-paste"></i>
                  </span>
                </button>
              </p>
            </span>
          </template>
        </Draggable>
      </div>
    </div>
  </div>
</template>
<script>
import "@he-tree/vue3/dist/he-tree-vue3.css";
import { Draggable } from "@he-tree/vue3";
import Data from "../../src/modified-input.json";
import { onMounted, ref } from "vue";
export default {
  components: { Draggable },
  setup() {
    // eslint-disable-next-line no-unused-vars
    const createUUID = () => {
      return "xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx".replace(
        /[xy]/g,
        function (c) {
          var r = (Math.random() * 16) | 0,
            v = c == "x" ? r : (r & 0x3) | 0x8;
          return v.toString(16);
        }
      );
    };

    // eslint-disable-next-line no-unused-vars
    const leftTree = ref(null);
    const inputData = ref(Data.BundlePro.Folder);
    const selectedId = ref("");
    const rightData = ref();
    const showFolder = ref(true);
    const cutNodeVal = ref(null);

    const dropChange = (event) => {
      // console.log('dropChange>event>', event)

      // Reconstruct existing data
      // console.log('new data>', event.targetTree.outputNestedData())
      inputData.value = event.targetTree.outputNestedData();
      rightData.value = null;
    };

    // eslint-disable-next-line no-unused-vars
    const iterateFolderOnly = (obj) => {
      Object.keys(obj).forEach((key) => {
        // console.log(`key: ${key}, value: ${obj[key]}`);

        if (typeof obj[key] === "object") {
          obj.id = createUUID();
          iterateFolderOnly(obj[key]);
        }
      });
    };

    // eslint-disable-next-line no-unused-vars
    const addLeftNode = (node, tree) => {
      console.log("node>", node);
      const name = prompt("Enter a name for the folder");
      if (!name) {
        return;
      }

      const newNode = {
        id: createUUID(),
        text: name,
        type: "folder",
        parentId: node.$id,
      };
      tree.addNode(newNode, node.id);

      setTimeout(() => {
        let list = document.getElementsByClassName("tree-node");
        Array.from(list).forEach(function (element) {
          if (element.innerText.trim().length == 0) {
            element.parentNode.removeChild(element);
          }
        }, 2000);
      });

      inputData.value = tree.outputNestedData();
      rightData.value = null;
    };

    // eslint-disable-next-line no-unused-vars
    const editLeftNode = (node, tree) => {
      const name = prompt("Enter a name for the folder");
      if (!name) {
        return;
      }

      node.text = name;
      inputData.value = tree.outputNestedData();
      rightData.value = null;
    };

    // eslint-disable-next-line no-unused-vars
    const deleteLeftNode = (node, tree) => {
      const del = confirm("Are you sure you want to delete this?");
      if (!del) {
        alert("Delete Cancelled");
        return;
      }

      tree.removeNode(node);
      rightData.value = null;
    };

    const addRightNode = (node, tree) => {
      const name = prompt("Enter a name for the Document");
      if (!name) {
        return;
      }
      const newNode = {
        id: createUUID(),
        text: name,
        type: "file",
        parentId: node.$id,
      };
      tree.addNode(newNode, node.id);
      rightData.value = tree.outputNestedData();
    };

    // eslint-disable-next-line no-unused-vars
    const editRightNode = (node, tree) => {
      const name = prompt("Enter a name for the Document");
      if (!name) {
        return;
      }
      node.text = name;
      rightData.value = tree.outputNestedData();
    };

    // eslint-disable-next-line no-unused-vars
    const deleteRightNode = (node, tree) => {};

    // eslint-disable-next-line no-unused-vars
    const cutRightNode = (node, tree) => {
      cutNodeVal.value = node;
      tree.removeNode(node);
    };

    // eslint-disable-next-line no-unused-vars
    const pasteRightNode = (node, tree) => {
      const newNode = {
        id: createUUID(),
        text: cutNodeVal.value.text,
        type: "file",
        parentId: node.$id,
      };
      tree.addNode(newNode, node.id);
      cutNodeVal.value = null;
    };

    // eslint-disable-next-line no-unused-vars
    const dropChangeRight = async (event) => {
      console.log("node drop", event);
      // TODO: Seems not acquiring latest tree data based on the node
      setTimeout(() => {
        // rightData.value = event.targetTree.outputNestedData();
      }, 5000);
    };

    const treeSelect = (id, node) => {
      rightData.value = null;
      setTimeout(() => {
        let list = document.getElementsByClassName("tree-node");
        Array.from(list).forEach(function (element) {
          if (element.innerText.trim().length == 0) {
            element.parentNode.removeChild(element);
          }
        }, 2000);
      });

      console.log("treeSelect>node>", node);
      selectedId.value = id;
      if (!node.$folded) {
        rightData.value = null;
      } else {
        rightData.value = node.children;
      }
    };

    const leftFolderOnly = ref([]);

    onMounted(() => {
      //   console.log("Drag>", leftTree.value);
      inputData.value.forEach((el) => {
        iterateFolderOnly(el);
      });
    });

    return {
      inputData,
      dropChange,
      dropChangeRight,
      selectedId,
      treeSelect,
      rightData,
      showFolder,
      addLeftNode,
      editLeftNode,
      deleteLeftNode,
      addRightNode,
      editRightNode,
      deleteRightNode,
      leftFolderOnly,
      cutRightNode,
      pasteRightNode,
      cutNodeVal,
    };
  },
};
</script>

<style>
.tree8:not(.he-tree-dragging) .tree-node-outer:hover {
  background: #ccc;
}
.tree8:not(.he-tree-dragging)
  .tree-node-outer:hover
  .tree-node:not(.tree-placeholder) {
  background: #ccc;
  border-color: #ccc;
}
.tree8 .tree-node:not(.tree-placeholder) {
  /* border: 1px solid #ccc;
  padding: 3px 10px;
  background: #0cec84; */
}
.tree8 .tree-node {
  /* border: 1px solid #ccc;
  padding: 2px 5px; */
}
.hide {
  display: none;
}
</style>