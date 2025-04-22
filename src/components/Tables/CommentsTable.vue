<template>
  <div class="comments-table">
    <table>
      <thead>
        <tr>
          <th>Comments</th>
          <th>Counsellor</th>
          <th>Date</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(comment, index) in paginatedComments" :key="index">
          <td>{{ comment.text }}</td>
          <td>{{ comment.counsellor }}</td>
          <td>{{ comment.date }}</td>
        </tr>
      </tbody>
    </table>

    <div class="pagination">
      <span
        v-for="page in totalPages"
        :key="page"
        class="page-number"
        :class="{ active: currentPage === page }"
        @click="goToPage(page)"
      >
        {{ page }}
      </span>
    </div>
  </div>
</template>

<script>
export default {
  name: "CommentsTable",
  props: {
    comments: {
      type: Array,
      required: true,
    },
    selectedCounsellor: {
      type: String,
      default: "All Counsellors",
    },
  },
  data() {
    return {
      currentPage: 1,
      perPage: 3,
    };
  },
  computed: {
    totalPages() {
      return Math.ceil(this.filteredComments.length / this.perPage);
    },
    filteredComments() {
      if (this.selectedCounsellor === "All Counsellors") {
        return this.comments;
      }
      return this.comments.filter(
        (comment) => comment.counsellor === this.selectedCounsellor
      );
    },
    paginatedComments() {
      const start = (this.currentPage - 1) * this.perPage;
      return this.filteredComments.slice(start, start + this.perPage);
    },
  },
  watch: {
    selectedCounsellor() {
      this.currentPage = 1;
    },
  },
  methods: {
    goToPage(page) {
      this.currentPage = page;
    },
  },
};
</script>

<style scoped>
table {
  font-family: "Assistant";
  width: 100%;
  border-collapse: collapse;
  background-color: white;
  height: 100%;
  border: 1px solid #ccc;
}
thead {
  background-color: #0288d1;
  color: white;
  text-align: left;
}

td,
th {
  padding: 7px;
  border: 1px solid #ddd;
  border-left: none;
  border-right: none;
  font-weight: 400;
}

.pagination {
  margin-top: 10px;
  display: flex;
  justify-content: flex-end;
  gap: 5px;
  padding-bottom: 10px;
}

.page-number {
  cursor: pointer;
  padding: 5px 10px;
  border: 1px solid #ddd;
  border-radius: 3px;
}

.page-number:hover {
  background-color: #f0f0f0;
}

.page-number.active {
  background-color: #007bff;
  color: white;
}
</style>
