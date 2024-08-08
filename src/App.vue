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
        if(newValue === "")
        this.getData();
      },
    }
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
    // Debounce function
    debounce(callback, waitTime) {
      let timer;
      return (...args) => {
          clearTimeout(timer);
          timer = setTimeout(() => {
              callback(...args);
          }, waitTime);
      }
    },
  
    getData() {
      fetch(`https://jsonplaceholder.typicode.com/photos`)
        .then((res) => res.json())
        .then((json) => {
          
          this.data = json.map((item) => {
            return {
              ...item,
              selected: false
            }
          });
        });
    },

    search(value) {
      fetch(`https://jsonplaceholder.typicode.com/photos?title=${value}`)
        .then((res) => res.json())
        .then((data) => this.data = data);
    },

    debouncedSearch: function(event) {
      if(event.target.value.length > 3)
      this.debounce(this.search(event.target.value), 300);
    },

    isObjectInArray(arr, key, value) {
      return arr.some(obj => obj[key] === value);
    },

    handleSelectItem(item,index){
      let newItem = {...item, selected: !item.selected}
      this.data[index].selected = !item.selected

      const exists = this.isObjectInArray(this.selectedItems, 'id', newItem.id);
      if(!exists){
        this.selectedItems = [...this.selectedItems, newItem]
      }else{
        this.selectedItems = this.selectedItems.filter((item) => item.id !== newItem.id)
      }
      
    }

   
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
        type="search"
        v-model="searchQuery"
        placeholder="Search Participant"
        @input="debouncedSearch"
      />
      <div class="suggestions">
        <div class="records-total">Showing 25 results</div>
        <ul>
          <li
            v-for="(item, index) in data"
            :key= "`rec ${item.id} ${index}`"
            @click="handleSelectItem(item, index)"
            :class="item.selected ? 'selected' : ''"
          >
            {{ item.title }}
          </li>
        </ul>
      </div>
    </div>
  </section>
</template>

<style scoped>

.records-total{
  background-color: #F1F1F1;
  height: 20px;
  padding: 5px;
  width: 380px;
  margin-top: 10px;
  color: gray;
}
.dropdown-wrapper {
  max-width: 400px;
  position: relative;
  margin: 0 auto;
}

.selected{
  background-color: teal;
  color: white
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
        border-bottom: 1px solid #F1F1F1;
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
