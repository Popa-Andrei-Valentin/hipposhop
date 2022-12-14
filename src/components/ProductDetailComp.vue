<template>
  <div class="product-container">
    <div class="product-details">
      <div class="closeModal">
        <p @click="closeModal()">&#9747;</p>
      </div>
      <div class="image">
        <img :src="getDetails._image" :alt="getDetails._title" />
      </div>
      <div class="modalDetailsContainer">
        <div class="title">
          <h2>{{ getDetails.title }}</h2>
          <p>{{ displayCategories }}</p>
        </div>
        <div class="pricingContainer">
          <div class="pricingQty">
            <p>Price: {{ getDetails.price }}/{{ getDetails.unit }}</p>
            <div class="qtyContainer">
              <p>
                Quantity:
                <input
                  class="cell"
                  type="number"
                  min="0"
                  v-model.number="quantity"
                />
              </p>
              <transition
                mode="in-out"
                enter-active-class="animate__animated animate__fadeIn"
                leave-active-class="animate__animated animate__fadeOut"
              >
                <p v-if="this.validQuantity" class="invalidQty">
                  Please select a quantity !
                </p>
              </transition>
            </div>
          </div>
          <div
            class="variantsContainer"
            v-if="Object.keys(getDetails.attributes).length > 0"
          >
            <div v-for="att in this.getAttList" :key="att">
              <p style="text-align: center">{{ att }}</p>
              <div>
                <button
                  v-for="items in this.getAttSets[att]"
                  @click="
                    loadDetails(items);
                    this.loadAttributesLists();
                  "
                  :class="
                    items.id !== getDetails.id ? 'variantsBtn' : 'selected'
                  "
                  :key="items.attributes.name"
                >
                  {{ items.attributes[att] }}
                </button>
              </div>
            </div>
          </div>
          <button class="addBtn" @click="addToCart(getDetails, this.quantity)">
            Add to Cart
          </button>
        </div>
        <div class="description">
          <h3>Product description</h3>
          <p>
            Lorem ipsum dolor sit amet, consectetur adipiscing elit. Quisque
            nisl eros, pulvinar facilisis justo mollis, auctor consequat urna.
            Morbi a bibendum metus. Donec scelerisque sollicitudin enim eu
            venenatis. Duis tincidunt laoreet ex, in pretium orci vestibulum
            eget. Class aptent taciti sociosqu ad litora torquent per conubia
            nostra, per inceptos himenaeos. Duis pharetra luctus lacus ut
            vestibulum. Maecenas ipsum lacus, lacinia quis posuere ut, pulvinar
            vitae dolor. Integer eu nibh at nisi ullamcorper sagittis id vel
            leo. Integer feugiat faucibus libero, at maximus nisl suscipit
            posuere. Morbi nec enim nunc. Phasellus bibendum turpis ut ipsum
            egestas, sed sollicitudin elit convallis. Cras pharetra mi tristique
            sapien vestibulum lobortis. Nam eget bibendum metus, non dictum
            mauris. Nulla at tellus sagittis, viverra est a, bibendum metus.
          </p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { mapActions, mapGetters } from "vuex";
// import {ProductTransformer} from "@/transformers/ProductTransformer";

export default {
  name: "ProductDetailComponent",
  emits: {
    // null -> No validation needed
    closeModal: null,
    addToCart: null,
  },
  data() {
    return {
      quantity: 0,
      validQuantity: false,
    };
  },
  computed: {
    ...mapGetters({
      getProducts: "products/getProducts",
      getDetails: "productDetail/getDetails",
      getAttList: "productDetail/getAttList",
      getAttSets: "productDetail/getAttSets",
      getCart: "cart/getCart",
    }),
    displayCategories() {
      let categories = this.getDetails.categories;
      if (categories.length > 1) {
        let display = "";
        for (let i = 0; i < categories.length; i++) {
          if (i === 0) {
            display += categories[i];
          }
          display += "-> " + categories[i];
        }
        return display;
      } else return categories[0];
    },
  },
  mounted() {
    this.loadAttributesLists();
  },
  methods: {
    ...mapActions({
      loadDetails: "productDetail/loadDetails",
      loadSelected: "cart/loadSelected",
      loadAttributesLists: "productDetail/loadAttributesLists",
      updateCart: "cart/updateCart",
    }),
    closeModal() {
      this.$emit("closeModal");
    },
    addToCart(selectedItem) {
      /**
       * Catch error: if local storage cart is empty
       */
      let item = selectedItem.toObject();
      if (this.quantity > 0) {
        item.showMessage = false;
        item.quantity = this.quantity;
        this.quantity = 0;
        this.loadSelected(item);
        this.validQuantity = false;
        this.$emit("addToCart", item);
      } else {
        this.validQuantity = true;
        setTimeout(() => {
          this.validQuantity = false;
        }, 4000);
      }
    },
  },
};
</script>

<style scoped>
@import "../assets/css/fontKanit.css";
@import "../assets/css/fontRoboto.css";
@import "../assets/css/fontPoppins.css";

.qtyContainer {
  position: relative;
}

.qtyContainer .invalidQty {
  width: 20ch;
  position: absolute;
  font-size: 1rem;
  color: red;
  top: 2rem;
  --animate-duration: 0.4s;
}

.product-container {
  position: absolute;
  height: 100vh;
  width: 100vw;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  background-color: rgba(0, 0, 0, 0.76);
  top: 0;
  left: 0;
  right: 0;
  margin-left: auto;
  margin-right: auto;
}

.variantsContainer {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 1rem;
}

.selected {
  color: black;
  border: 2px black solid;
  background-color: gray;
  padding: 0.5rem;
  font-weight: bold;
  font-family: "Poppins", sans-serif;
  margin: 0.2rem;
  pointer-events: none;
}

.variantsBtn {
  color: black;
  border: 2px black solid;
  background-color: white;
  padding: 0.5rem;
  font-weight: bold;
  font-family: "Poppins", sans-serif;
  margin: 0.2rem;
  cursor: pointer;
}

.product-details {
  width: 50vw;
  height: auto;
  max-height: 80vh;
  background-color: white;
  border-radius: 10px;
  padding: 1rem;
  display: grid;
  grid-template:
    "closeButton" 65px
    "productImage" auto
    "content" auto
    / auto;
  overflow: auto;
}

.product-details::-webkit-scrollbar {
  width: 12px;
}

.product-details::-webkit-scrollbar-track {
  background-color: #e4e4e4;
  border-radius: 100px;
}

.product-details::-webkit-scrollbar-thumb {
  background-color: rgb(16, 191, 255);
  border-radius: 100px;
}

.closeModal {
  width: 100%;
  grid-area: closeButton;
  display: flex;
  justify-content: flex-end;
}

.closeModal p {
  margin-right: 1rem;
  padding: 0.2rem;
  color: white;
  background-color: #2095e1ff;
  cursor: pointer;
}

.closeModal p:hover {
  background-color: #0e3756;
}

.image {
  grid-area: productImage;
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}

.image img {
  width: auto;
  height: auto;
  max-height: 15rem;
}

.modalDetailsContainer {
  grid-area: content;
  display: grid;
  grid-template:
    "title" 120px
    "pricing" auto
    "description" auto
    / auto;
}

.title {
  margin: 0;
  width: 100%;
  height: 100%;
  grid-area: title;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.title p {
  color: rgb(16, 191, 255);
  font-size: 0.9rem;
  margin: 0;
}

.title h2 {
  text-align: center;
  margin: 0;
}

.pricingContainer {
  grid-area: pricing;
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
}

.pricingContainer .addBtn {
  background-color: #2095e1ff;
  padding: 0.6rem;
  font-size: 1rem;
  color: white;
  text-transform: uppercase;
  font-weight: bold;
  border-radius: 0.7rem;
  border: none;
  text-decoration: none;
}

.pricingContainer .addBtn:hover {
  background-color: rgb(7, 72, 96);
  cursor: pointer;
}

.pricingQty {
  display: flex;
  width: 40%;
  justify-content: space-between;
  align-items: center;
}

.pricingQty input {
  padding: 0.2rem;
  font-weight: bold;
  font-family: "Poppins", sans-serif;
  width: 2.5rem;
  border-radius: 6px;
  border: 1px solid black;
}

.pricingQty input:hover {
  border: 2px solid #2095e1ff;
}

.pricingQty input:focus {
  border: 2px solid #2095e1ff;
}

.description {
  grid-area: description;
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
}

.description h3 {
  text-align: center;
}
</style>
