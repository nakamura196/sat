<template>
  <div>
    <v-container>
      <h2 class="my-5">
        {{ $t('search_result') }}
      </h2>

      <h3 class="my-5">{{ total.toLocaleString() }}{{ $t('hits') }}</h3>

      <div class="text-center mb-5">
        <v-pagination
          v-model="currentPage"
          :length="paginationLength"
          :total-visible="7"
          @input="setCurrentPage"
        ></v-pagination>
      </div>

      <table
        border="1"
        style="border-collapse: collapse;"
        width="100%"
        class="my-2"
      >
        <tr>
          <th colspan="7">
            {{ '基本情報' }}
          </th>
          <th colspan="4">
            {{ '勘同目録' }}
          </th>
          <th colspan="4">
            {{ '脚注' }}
          </th>
          <th rowspan="2">詳細情報</th>
        </tr>
        <tr>
          <th>{{ '経典番号' }}</th>
          <th>{{ '枝番' }}</th>
          <th>{{ '経典名' }}</th>
          <th>{{ '収録巻次' }}</th>
          <th>{{ '部門' }}</th>
          <th>{{ '配本' }}</th>
          <th>{{ '出版年月日' }}</th>

          <th>{{ '底本/校本' }}</th>
          <th>{{ '❹' }}</th>
          <th>{{ '❼' }}</th>
          <th>{{ '❼備考' }}</th>

          <th>{{ '底本/校本' }}</th>
          <th>{{ '新添部分' }}</th>
          <th>{{ 'テキスト' }}</th>
          <th>{{ '備考' }}</th>
        </tr>
        <tr
          v-for="(obj, index) in displayResults"
          :key="index"
          class="text-center"
        >
          <td width="5%">{{ obj['基-経典番号'] }}</td>
          <td width="2%">{{ obj['基-枝番'] }}</td>
          <td width="10%">
            <a
              :href="
                'https://21dzk.l.u-tokyo.ac.jp/SAT2018/' +
                obj['sat_id'] +
                '.html'
              "
              target="_blank"
              >{{ obj['基-経典名'] }}</a
            >
          </td>
          <td width="2%">{{ obj['基-収録巻次'] }}</td>
          <td width="5%">{{ obj['基-部門'] }}</td>
          <td width="2%">{{ obj['基-配本'] }}</td>
          <td width="5%">{{ obj['基-年月日'].join(', ') }}</td>
          <td
            width="5%"
            :bgcolor="
              obj['勘-底本/校本'] == '底本'
                ? '#BBDEFB'
                : obj['勘-底本/校本'] == '校本'
                ? '#FFCDD2'
                : ''
            "
          >
            {{ obj['勘-底本/校本'] }}
          </td>
          <td width="10%">{{ obj['勘-❹'] }}</td>
          <td width="10%">{{ obj['勘-❼'] }}</td>
          <td width="10%">{{ obj['勘-❼備考'] }}</td>

          <td
            width="5%"
            :bgcolor="
              obj['脚-底本/校本'] == '底本'
                ? '#BBDEFB'
                : obj['脚-底本/校本'] == '校本'
                ? '#FFCDD2'
                : ''
            "
          >
            {{ obj['脚-底本/校本'] }}
          </td>
          <td width="5%">{{ obj['脚-新添部分'] }}</td>
          <td width="10%">{{ obj['脚-テキスト'] }}</td>
          <td width="10%">{{ obj['脚-備考'] }}</td>

          <td width="5%">
            <nuxt-link
              target="_blank"
              :to="
                localePath({
                  name: 'item-id',
                  params: {
                    id: ('00000' + obj['No.']).slice(-5),
                  },
                })
              "
              >{{ 'more' }}
            </nuxt-link>
          </td>
        </tr>
      </table>

      <v-data-table
        v-if="false"
        :headers="headers"
        :items="desserts"
        :items-per-page="5"
        multi-sort
      >
        <template v-slot:item.title="{ item }">
          <a :href="satUrl(item)" target="blank">{{ item.title }}</a>
        </template>

        <template v-slot:item.kt="{ item }">
          <v-chip :color="item.kt == '底本' ? 'error' : 'primary'" dark>{{
            item.kt
          }}</v-chip>
        </template>

        <template v-slot:item.ft="{ item }">
          <v-chip :color="item.ft == '底本' ? 'error' : 'primary'" dark>{{
            item.ft
          }}</v-chip>
        </template>

        <template v-slot:item.no="{ item }">
          <nuxt-link
            :to="
              localePath({
                name: 'item-id',
                params: {
                  id: item.no,
                },
              })
            "
            >{{ 'more' }}
          </nuxt-link>
        </template>
      </v-data-table>

      <v-card v-if="false">
        <v-card-title>
          <v-spacer></v-spacer>
          <!-- 
          <v-text-field
            v-model="search"
            append-icon="mdi-magnify"
            :label="$t('search')"
            single-line
            hide-details
          ></v-text-field>
          -->
        </v-card-title>
      </v-card>
    </v-container>
  </div>
</template>

<script lang="ts">
import { Vue, Watch, Component /*, Watch */ } from 'nuxt-property-decorator'
// import axios from 'axios'

// import searchResult from '~/components/ui/searchResult.vue'
import CardItem from '~/components/display/CardItem.vue'

@Component({
  components: {
    // searchResult
    CardItem,
  },
})
export default class Volumes extends Vue {
  search: string = ''
  headers: any[] = [
    {
      text: '経典番号',
      value: '基本情報-経典番号',
      filterable: false,
    },
    { text: '枝番', value: '基本情報-枝番', filterable: false },
    { text: '経典名', value: 'title', filterable: false },
    { text: '収録巻次', value: '基本情報-収録巻次', filterable: false },
    { text: '部門', value: '基本情報-部門', filterable: false },
    { text: '配本', value: '基本情報-配本', filterable: false },
    { text: '出版年月日', value: '基本情報-出版年月日', filterable: false },
    { text: '底本/校本', value: 'kt', filterable: false },
    { text: '④', value: '勘同目録-④', filterable: false },
    { text: '⑦', value: '勘同目録-⑦', filterable: false },
    { text: '⑦備考', value: '勘同目録-⑦備考', filterable: false },
    { text: '底本/校本', value: 'ft', filterable: false },
    { text: '新添部分', value: '脚注-新添', filterable: false },
    { text: 'テキスト', value: '脚注-テキスト', filterable: false },
    { text: '備考', value: '脚注-備考', filterable: false },
    { text: '詳細情報', value: 'no' },
  ]

  currentPage: number = 1
  size: number = 50

  get total() {
    return this.searchResults.length
  }

  get paginationLength() {
    return Math.ceil(this.total / this.size)
  }

  displayResults: any[] = []
  searchResults: any[] = []

  async asyncData(context: any) {
    const uri = process.env.BASE_URL + '/index.json'
    const apiResult = await context.$axios
      .get(uri)
      .then((response: any) => {
        const apiResult = response.data
        return apiResult
      })
      .catch((error: any) => {
        // eslint-disable-next-line
        console.error(error)
      })

    const from: number = context.query.from ? Number(context.query.from) : 0

    const currentPage = Math.round(from / 50) + 1

    return {
      currentPage,
      searchResults: apiResult,
    }
  }

  created() {
    this.main()
  }

  main() {
    const from: number = (this.currentPage - 1) * this.size
    this.displayResults = this.searchResults.slice(from, from + this.size)
  }

  @Watch('$route')
  watchRoute() {
    this.main()
  }

  setCurrentPage() {
    if (this.currentPage > 0) {
      const query: any = Object.assign({}, this.$route.query)
      query.from = (this.currentPage - 1) * this.size
      this.updateQuery(query)
    }
  }

  updateQuery(query: any) {
    this.$router.push(
      this.localePath({
        name: 'search',
        query,
      }),
      () => {},
      () => {}
    )
  }

  satUrl(data: any): string {
    return (
      'https://21dzk.l.u-tokyo.ac.jp/SAT2018/' +
      data['基本情報-経典番号'] +
      '_.' +
      ('00' + data['SAT頭出し用']['開始巻']).slice(-2) +
      '.' +
      ('0000' + data['SAT頭出し用']['ページ']).slice(-4) +
      data['SAT頭出し用']['段'] +
      ('00' + data['SAT頭出し用']['行']).slice(-2) +
      '.html'
    )
  }
}
</script>
