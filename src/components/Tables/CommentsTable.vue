<template>
  <div class="comments-table">
    <table>
      <thead>
        <tr>
          <th>Comment</th>
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
      <button @click="prevPage" :disabled="currentPage === 1">Prev</button>
      <span>Page {{ currentPage }} of {{ totalPages }}</span>
      <button @click="nextPage" :disabled="currentPage === totalPages">
        Next
      </button>
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
    nextPage() {
      if (this.currentPage < this.totalPages) {
        this.currentPage++;
      }
    },
    prevPage() {
      if (this.currentPage > 1) {
        this.currentPage--;
      }
    },
  },
};
</script>

<style scoped>
.comments-table {
  padding: 1rem;
}
table {
  width: 100%;
  border-collapse: collapse;
}
thead {
  background-color: #f0f0f0;
}
td,
th {
  padding: 0.5rem;
  border: 1px solid #ddd;
}
.pagination {
  margin-top: 1rem;
  display: flex;
  justify-content: space-between;
  align-items: center;
}
</style>
