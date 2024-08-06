<script>
export default {
  name: "Autocomplete with Typeahead",
  data() {
    return {
      searchQuery: "",
      selectedItems: [],
      isShowing: false,
      displayLabel: "Select Participant",
      data: [],
    };
  },
  watch: {
    selectedItems: {
      handler(newValue) {
        console.log(newValue);
      },
    },
    searchQuery: {
      handler(newValue) {
        console.log(newValue);
      },
    },
  },
  computed: {
    filtered() {
      const query = this.searchQuery.toLowerCase();
      if (this.searchQuery === "") {
        return this.data;
      }
      //return this.data.filter((user) => user.name.includes(query))
      return this.data.filter((item) => {
        return Object.values(item).some((word) =>
          String(word).toLowerCase().includes(query)
        );
      });
    },
    displayLabel() {
      if (this.selectedItems.length == 0) {
        return "Select Participant";
      } else if (this.selectedItems.length == 1) {
        return this.selectedItems[0].title;
      } else {
        return this.selectedItems.length + " items selected";
      }
    },
  },
  methods: {
    getData() {
      fetch("https://jsonplaceholder.typicode.com/photos")
        .then((res) => res.json())
        .then((json) => {
          console.log(json);
          this.data = json;
        });
    },
  },
  mounted() {
    this.getData();
  },
};
</script>

<template>
  <section class="dropdown-wrapper">
    <div class="selected-item" @click="isShowing = !isShowing">
      <span>{{ displayLabel }}</span>
      <svg
       :class="isShowing ? 'dropdown' : ''"
        class="drop-down-icon"
        xmlns="http://www.w3.org/2000/svg"
        width="24"
        height="24"
        viewBox="0 0 24 24"
        fill="currentColor"
      >
        <path
          d="M11.9999 10.8284L7.0502 15.7782L5.63599 14.364L11.9999 8L18.3639 14.364L16.9497 15.7782L11.9999 10.8284Z"
        ></path>
      </svg>
    </div>

    <div class="dropdown-popover" v-if="isShowing">
      <input
        type="text"
        v-model="searchQuery"
        placeholder="Search Participant"
      />
      <div class="suggestions">
        <ul>
          <li
            v-for="(item, index) in filtered"
            :key="item.id"
            @click="selectedItems = [...selectedItems, item]"
          >
            {{ item.title }}
          </li>
        </ul>
      </div>
    </div>
  </section>
</template>

<style scoped>
.dropdown-wrapper {
  max-width: 400px;
  position: relative;
  margin: 0 auto;
}

.selected-item {
  border-radius: 5px;
  border: 2px solid teal;
  height: 40px;
  width: 400px;
  padding: 5px 10px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  font-size: medium;
  font-weight: 500;

  .drop-down-icon {
    transform: rotate(0deg);
    transition: all 0.5s ease;
    &.dropdown {
      transform: rotate(180deg);
    }
  }
}

.dropdown-popover {
  position: absolute;
  left: 0;
  right: 0;
  border: 2px solid teal;
  width: 400px;
  padding: 5px 10px;
  background-color: white;

  input {
    width: 98%;
    height: 30px;
    border: 2px solid lightgray;
    padding-left: 5px;
    border-radius: 5px;
  }

  .suggestions {
    width: 100%;

    ul {
      list-style: none;
      text-align: left;
      padding-left: 5px;
      line-height: 1.5rem;
      max-height: 300px;
      overflow-y: scroll;
      /* border: 1px solid lightgray; */

      li {
        width: 100%;
        border-bottom: 1px solid teal;
        padding: 10px;
        cursor: pointer;
        font-size: medium;
        &:hover {
          background: teal;
          color: #fff;
          font-weight: bold;
        }
      }
    }
  }
}
</style>
