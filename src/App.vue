<template>
  <div>
    <div class="search-area">
      <input
        v-model="searchQuery"
        type="text"
        placeholder="Search for accommodations..."
        class="search-input"
      />
      <input
        v-model.number="filterBeds"
        type="text"
        placeholder="Text for Beds"
        class="filter-input"
      />
      <input
        v-model.number="filterRooms"
        type="text"
        placeholder="Text for Rooms"
        class="filter-input"
      />
      <input
        v-model.number="filterPeople"
        type="text"
        placeholder="Text for People"
        class="filter-input"
      />
      <div class="filter-container">
        <label class="filter-label">Max Price:</label>
        <input
          type="range"
          v-model="filterMaxPrice"
          :min="15"
          :max="10000"
          class="range-input"
        />
        <span class="range-value">{{ filterMaxPrice }}</span>
      </div>
      <button @click="fetchListings" class="search-button">Search</button>
    </div>
    <div v-if="listings.length<1" ><h1>No Apartments</h1></div>
    <div v-else="listings.length>0" class="grid" :class="{ 'grid-disabled': loading }">
      
     
      
      <div   v-for="listing in listings" :key="listing.id" class="listing">
        
        <img :src="listing.images?.picture_url" alt="Listing image">
        <h2>{{ listing.name }}</h2>
        <p v-if="!showMore[listing.id]">{{ shortDescription(listing.description) }}</p>
        <p v-else>{{ listing.description }}</p>
        <button @click="toggleShowMore(listing.id)">{{ showMore[listing.id] ? 'Read Less' : 'Read More' }}</button>
        <p><b>City</b>: {{ listing.address.market }}</p>
        <p><b>Rooms</b>: {{ listing.bedrooms }}</p>
        <p><b>Price</b>: ${{ listing.price }}</p>
        <p><b>Beds</b>: {{ listing.beds }}</p>
        <p><b>People</b>: {{ listing.accommodates }}</p>
        <p><b>Rating</b>: {{listing.review_scores.review_scores_rating }}</p>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      searchQuery: '',
      listings: [],
      filterBeds: '',
      filterRooms: '',
      filterPeople: '',
      loading: false,
      showMore: {},
      filterMaxPrice: 10000,
    }
  },
  mounted() {
    this.fetchListings();
  },
  methods: {
    // TODO: Replace <APP_SERVICES_HTTP_GET_ENDPOINT> with the relevant endpoint
    async fetchListings() {
      try {
        this.loading = true;
        const params = {
          search: this.searchQuery,
          beds: this.filterBeds,
          rooms: this.filterRooms,
          people: this.filterPeople,
          // maxPrice: this.filterMaxPrice,
        };
        const response = await axios.get(
          '<APP_SERVICES_HTTP_GET_ENDPOINT>',
          { params }
        );
        this.loading = false;
        this.listings = response.data;

        // Initialize showMore for each listing
        this.listings.forEach(listing => {
          this.showMore[listing.id] = false;
        });
      } catch (error) {
        console.error(error);
      }
    },
    shortDescription(description) {
      if (description.length > 100) {
        return description.substring(0, 100) + '...';
      } else {
        return description;
      }
    },
    toggleShowMore(id) {
      this.showMore[id] = !this.showMore[id];
    },
  },
}
</script>


<style scoped>
.search-input, .search-button {
  margin: 10px;
  
}

.search-area {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  gap: 10px;
}

.search-input {
  padding: 10px;
  border-radius: 8px;
  border: 1px solid #ddd;
  width: 70%;
}

.grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
  gap: 20px;
  padding: 10px;
}

.listing {
  border: 1px solid #ddd;
  padding: 10px;
  border-radius: 8px;
  overflow: hidden;
}

.listing img {
  width: 100%;
  height: 200px;
  object-fit: cover;
  border-radius: 8px;
}

.grid-disabled {
  animation: opacityLoop 4s infinite;
  /* Adjust the blur radius as needed */
}

@keyframes opacityLoop {
  0%, 100% {
    opacity: 0.5;
    backdrop-filter: blur(50px); 
  }
  50% {
    opacity: 1;
    backdrop-filter: blur(50px); 
  }
}
</style>