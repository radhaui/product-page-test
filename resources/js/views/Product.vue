<template>
    <div class="product-page d-flex">
        <div v-if="loading">Loading...</div>
        <div v-else-if="data" class="product-container d-flex">
            <div class="product-img">
                <img :src="imageLink" alt="Dynamic Image" v-if="imageLink"/>
                <div class="product-image-gallary d-flex">
                    <img 
                        v-for="(image, index) in data.images" 
                        :key="index" 
                        :src="image" 
                        alt="gallary imgages" 
                        :class="selectedIndex === index ? 'selected-img' : '' "
                        @click="updateImageLink(index)"

                    />
                </div>
            </div>

            <div class="product-details">
                <h1>{{data.name}}</h1>
                <p>{{ data.description }}</p>
                <div><span class="product-discounted">$ {{ data.price?.discounted}}.00</span><span class="discount-badge">{{ data.discount?.amount}}%</span></div>
                <div class="full-price">${{ data.price?.full}}.00</div>
                <div class="d-flex">
                    <div class="btn-group d-flex">
                        <button @click="itemsToAdd('sub')" :disabled="itemsToAddCart<1">-</button>
                        <div>{{itemsToAddCart}}</div>
                        <button @click="itemsToAdd('add')">+</button>
                    </div>
                    <button class="add-to-cart d-flex"> <span class="cart"></span><span>Add to cart</span></button>

                </div>
            </div>
        </div>
    </div>
</template>

<script>
import { ref, onMounted } from 'vue';
export default {
    name: 'Product',
  setup() {
    const data = ref({});
    const loading = ref(false);
    const error = ref(null);
    const imageLink = ref('');
    const selectedIndex = ref(0);
    const itemsToAddCart = ref(0);

    const fetchData = async () => {
      loading.value = true;
      error.value = null;
      try {
        const response = await fetch('/client/products/fall-limited-edition-sneaker');
        if (!response.ok) {
            alert("erroe");
          throw new Error('Network response was not ok');
        }
        const result = await response.json();
        data.value = result.data;
        imageLink.value = result.data.images[0];
      } catch (err) {
        error.value = err.message;
      } finally {
        loading.value = false;
      }
    };

    onMounted(() => {
      fetchData();
    });

    const updateImageLink = (index) => {
     imageLink.value = data.value.images[index];
     selectedIndex.value = index;
    };
    const itemsToAdd = (type) => {
        itemsToAddCart.value = type === "add" ? itemsToAddCart.value+1 : itemsToAddCart.value-1;
        
    };


    return {
      data,
      loading,
      error,
      imageLink,
      updateImageLink,
      selectedIndex,
      itemsToAddCart,
      itemsToAdd
    };
  }
};
</script>