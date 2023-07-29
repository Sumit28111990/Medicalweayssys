<template>
  <div>
    <div class="col-xl-12">
      <div class="card">
        <div class="card-body">
          <div class="product-info">
            <form @submit.prevent="submitForm">
              <div>
                <label for="username">UserName</label>
                <input
                  class="form-control"
                  type="text"
                  id="username"
                  placeholder="Enter Username"
                  v-model="ratingObject.userName"
                />
                <span class="text-danger" v-if="errors.userName !== ''">
                  {{ errors.userName }}
                </span>
              </div>
              <div>
                  <label for="rating">Rating</label>
                  <input
                    class="form-control"
                    type="number"
                    id="rating"
                    placeholder="Enter Product Rating Only Integer Value"
                    v-model="ratingObject.ratingNumber"
                  />
                  <span class="text-danger" v-if="errors.ratingNumber !== ''">
                    {{ errors.ratingNumber }}
                  </span>
                </div>
                <div>
                  <label for="location">Location</label>
                  <input
                    class="form-control"
                    type="text"
                    id="location"
                    placeholder="Enter Location"
                    v-model="ratingObject.location"
                  />
                  <span class="text-danger" v-if="errors.location !== ''">
                    {{ errors.location }}
                  </span>
                </div>
  
                <div>
                  <label for="desc">Review</label>
                  <textarea
                    class="form-control"
                    placeholder="Enter Review"
                    v-model="ratingObject.review"
                    name="w3review"
                    rows="4"
                    cols="50"
                  ></textarea>
                  <span class="text-danger" v-if="errors.review !== ''">
                    {{ errors.review }}
                  </span>
                </div>

              <button class="mt-4 ml-4 btn btn-primary radius" type="submit">
                Submit
              </button>
            </form>
          </div>
        </div>
      </div>
    </div>

    <!-- Table to display reviews -->
    <!-- <table class="table mt-4">
      <thead>
        <tr>
          <th>S/N</th>
          <th>User Name</th>
          <th>Rating</th>
          <th>Location</th>
          <th>Review</th>
          <th>Action</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(review, index) in reviews" :key="index">
          <td>{{ index+1 }}</td>
          <td class="capitalize">{{ review.user_name }}</td>
          <td class="capitalize">{{ review.rating }}</td>
          <td class="capitalize">{{ review.location }}</td>
          <td class="capitalize">{{ review.review }}</td>
          <td>
            <button class="btn btn-sm btn-primary" @click="editReview(index)">
              Edit
            </button>
            <button class="btn btn-sm btn-danger" @click="deleteReview(index)">
              Delete
            </button>
          </td>
        </tr>
      </tbody>
    </table> -->
  </div>
</template>

<script>
import config from "../../config";
import axios from "axios";

export default {
  data() {
    return {
      EditIds: null,
      parentId: "",
      ratingObject: {
        userName: "",
        ratingNumber: null,
        location: "",
        review: "",
      },
      errors: {
        userName: "",
        ratingNumber: "",
        location: "",
        review: "",
      },
      reviews: [], // Array to store submitted reviews
    };
  },
  methods: {
    submitForm() {
      this.errors = {
        userName: "",
        ratingNumber: "",
        location: "",
        review: "",
      };

      if (!this.ratingObject.userName) {
        this.errors.userName = "The userName Field is required";
      }
      // Validate other form fields...

      if (Object.values(this.errors).some((error) => error !== "")) {
        return;
      }

      const data = {
        user_name: this.ratingObject.userName,
        rating: this.ratingObject.ratingNumber,
        location: this.ratingObject.location,
        review: this.ratingObject.review,
        parent_id: this.parentId,
      };

      let token = localStorage.getItem("token");

      axios
        .post(`${config.apiUrl}/api/my-product-review`, data, {
          headers: {
            Authorization: `Bearer ${token}`,
            "Content-Type": "application/json",
          },
        })
        .then((res) => {
          console.log(res, "res");
          console.log(res.data);
          window.location.reload()
          // Add the submitted review to the reviews array
        
          this.ratingObject = {
            userName: "",
            ratingNumber: null,
            location: "",
            review: "",
          };
        })
        .catch((error) => {
          console.error(error);
        });
    },

    editReview(index) {
      this.ratingObject = { ...this.reviews[index] };
    },

    deleteReview(index) {
      // let token = localStorage.getItem("token");

      // axios
      //   .delete(`${config.apiUrl}/api/my-product-review/${this.reviews[index].id}`, {
      //     headers: {
      //       Authorization: `Bearer ${token}`,
      //     },
      //   })
      //   .then(() => {
      //     this.reviews.splice(index, 1);
      //   })
      //   .catch((error) => {
      //     console.error(error);
      //   });
    },

    getAllReviews(id) {
      let token = localStorage.getItem("token");

      axios
        .get(`${config.apiUrl}/api/my-product-get-review/${id}`, {
          headers: {
            Authorization: `Bearer ${token}`,
          },
        })
        .then((res) => {
          // console.log(res,'res')
          this.reviews = res.data.result;
          // console.log(this.reviews,'rej')
        })
        .catch((error) => {
          console.error(error);
        });
    },

    // Your getFormDataW method...

    // Your other methods...
  },
  mounted() {
   

    const url = new URL(window.location.href);
    const id = url.pathname.split("/").pop();
    const isNum = /^\d+$/.test(id);
    if (isNum) {
      // this.getFormDataW(id);
      this.getAllReviews(id);
      this.parentId = id;
    }
  },
};
</script>

<style scoped>
/* Your styles here... */
.radius {
  border-radius: 22px !important;
}
.capitalize {
    text-transform: capitalize !important;
   
}
</style>
