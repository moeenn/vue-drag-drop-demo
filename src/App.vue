<template>
  <div>
    <div id="main">
      <template v-for="item of items">
        <div
          :key="item.id"
          draggable="true"
          :data-item-id="item.id"
          :class="{ draggable: true, dragged: item.drag }"
          :style="{
            'background-image': 'url(' + item.image + ')',
            'background-size': 'cover',
            cursor: item.drag ? 'grabbing' : 'move',
          }"
          @dragstart="handleDragStart(item)"
          @drag="handleDrag($event)"
          @dragend="handleDrop"
        ></div>
      </template>
    </div>

    <button @click="getOrder">Get Order</button>
  </div>
</template>

<script>
export default {
  name: "app",
  data() {
    return {
      items: [
        {
          id: 1,
          image: "./src/assets/1.jpeg",
        },
        {
          id: 2,
          image: "./src/assets/2.jpeg",
        },
        {
          id: 3,
          image: "./src/assets/3.jpeg",
        },
        {
          id: 4,
          image: "./src/assets/4.jpeg",
        },
        {
          id: 5,
          image: "./src/assets/5.jpeg",
        },
        {
          id: 6,
          image: "./src/assets/6.jpeg",
        },
      ],

      drag: {
        currentItem: null,
        dropTarget: -1, // dragged item will be inserted before item of this position index
      },

      initDragState: null,
    };
  },

  mounted() {
    this.initDragState = { ...this.drag };
  },

  methods: {
    setItems(items) {
      this.items = [...items];
    },

    resetDragState() {
      this.drag = { ...this.initDragState };
    },

    setDragStatus(item, status) {
      const items = this.items.map((i) => {
        if (i.id === item.id) i.drag = status;
        return i;
      });

      this.setItems(items);
    },

    handleDragStart(item) {
      this.setDragStatus(item, true);
      this.drag.currentItem = { ...item };
    },

    handleDrag(event) {
      const currentDropTarget = this.getDropTarget(event);
      if (currentDropTarget !== -1) this.drag.dropTarget = currentDropTarget;
    },

    handleDrop() {
      this.drag.currentItem.drag = false;
      const items = this.items.filter((i) => !i.drag);

      if (this.drag.dropTarget === -1) {
        items.push(this.drag.currentItem);
      }

      if (this.drag.dropTarget !== -1) {
        items.splice(this.drag.dropTarget, 0, this.drag.currentItem);
      }

      this.setItems(items);
      this.resetDragState();
    },

    /**
     *  returns position (insert index) for the drop item
     */
    getDropTarget({ clientX, clientY }) {
      const swapElement = document.elementFromPoint(clientX, clientY);

      /**
       *  rewrite: swapElement?.dataset?.itemId
       */
      if (swapElement) {
        if (swapElement.dataset) {
          if (swapElement.dataset.itemId) {
            const id = swapElement.dataset.itemId;
            return this.items.findIndex((i) => i.id == id);
          }
        }
      }

      return -1;
    },

    getOrder() {
      const result = this.items.map((item, index) => {
        return {
          id: item.id,
          order: index,
        };
      });

      console.log("Order:", result);
    },
  },
};
</script>

<style>
*,
::before,
::after {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-weight: normal;
}

html,
body {
  height: 100vh;
  width: 100vw;
}

body {
  padding: 3rem;
  font-family: Arial, Helvetica, sans-serif;
}

#main {
  background: rgba(0, 0, 0, 0.05);
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  padding: 2rem;
  gap: 2rem;
  border-radius: 0.4rem;
}

.draggable {
  height: 9rem;
  width: 15rem;
  border-radius: 0.4rem;
  box-shadow: 0.1rem 0.2rem 0.3rem rgba(0, 0, 0, 0.1);
}

.dragged {
  opacity: 0.5;
}

button {
  margin-top: 1rem;
  padding: 0.5rem 0.7rem;
  border: none;
  border-radius: 0.2rem;
  cursor: pointer;
}
</style>
