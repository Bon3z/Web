<template>
  <el-row v-if="dataLoaded" :gutter='24'>
    <el-col class='book-bubble' :offset='8'>
      <el-col class='book-cover' :span='8'>
        <el-image :src="require('@/assets/logo.png')"></el-image>
      </el-col>
      <el-col :span='16'>
        <book-display :book="book"
                      :author="author"
                      :category="category"
        ></book-display>
      </el-col>
    </el-col>
    <el-col class='book-bubble' :offset='8'>
      <el-row>
        <rate-book :book-id='bookId'></rate-book>
      </el-row>
      <hr>
      <el-row>
        <h1>{{ $t('book.review') }}</h1>
        <review-card :book-id='bookId'></review-card>
      </el-row>
    </el-col>
  </el-row>
</template>

<script lang='ts'>
import { Vue, Component } from 'vue-property-decorator'
import { Book } from '@/models/book'
import axios from 'axios'
import { Author } from '@/models/author'
import { Category } from '@/models/category'
import RateBook from '@/views/book/content/RateBook.vue'
import ReviewCard from '@/views/book/content/ReviewCard.vue'
import BookDisplay from '@/components/BookDisplay.vue'
import { UserStoreMethods } from '@/enums/UserStoreMethods'
import { Getter } from 'vuex-class'

@Component({
  components: { BookDisplay, RateBook, ReviewCard }
})
export default class BookCard extends Vue {
  private bookId: string = this.$route.params.id
  private book: Book | null = null
  private author: Author | null = null
  private category: Category | null = null

  async created (): Promise<void> {
    await this.getBook()
    await this.getAuthor()
    await this.getCategory()
  }

  async getBook (): Promise<void> {
    await axios.get(`Books/${this.bookId}`).then(
      (response) => {
        this.book = response.data.content
      }
    )
  }

  async getAuthor (): Promise<void> {
    await axios.get(`Authors/${this.book?.authorId}`).then(
      (response) => {
        this.author = response.data.content
      }
    )
  }

  async getCategory (): Promise<void> {
    await axios.get(`Category/${this.book?.categoryId}`).then(
      (response) => {
        this.category = response.data.content
      }
    )
  }

  get dataLoaded (): boolean {
    return this.category !== null && this.author !== null && this.book !== null
  }

  @Getter [UserStoreMethods.getUserRole]: string | Array<string>
}
</script>
