<template>
  <div>
    <SearchForm />

    <div style="background-color: #eeeeee;">
      <v-container class="pt-5">
        <v-row no-gutters align="center">
          <v-col cols="12" sm="5">
            <h3 class="my-5">{{ total.toLocaleString() }}{{ $t('hits') }}</h3>
          </v-col>
          <v-col cols="12" sm="7">
            <v-row dense>
              <template>
                <v-col cols="12" sm="3">
                  <v-select
                    v-model="sort"
                    :items="computedItemsSort"
                    :label="$t('Sort by')"
                    @change="setSort"
                  ></v-select>
                </v-col>

                <v-col cols="12" sm="3" dense>
                  <v-select
                    v-model="size"
                    :items="[
                      { value: 20, text: '20' },
                      { value: 50, text: '50' },
                      { value: 100, text: '100' },
                      { value: 500, text: '500' },
                    ]"
                    :label="$t('items_per_page')"
                    @change="setSize"
                  ></v-select>
                </v-col>
              </template>
            </v-row>
          </v-col>
        </v-row>
      </v-container>

      <SearchFilter />
    </div>

    <v-container v-show="!loadingFlag" class="mt-5">
      <v-row>
        <v-col cols="12" :sm="facetFlag ? 8 : 12" order-sm="12">
          <h3 class="mb-5">
            <v-tooltip bottom>
              <template v-slot:activator="{ on }">
                <i
                  v-show="!facetFlag"
                  style="cursor: pointer;"
                  class="far fa-caret-square-right mr-2"
                  v-on="on"
                  @click="facetFlag = !facetFlag"
                ></i>
              </template>
              <span>{{ $t('open_facets') }}</span>
            </v-tooltip>
            {{ $t('search_result') }}
          </h3>

          <p class="text-right">
            <a>検索結果に関する統計情報を見る</a>
          </p>

          <template v-if="total > 0">
            <div class="text-center">
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
                v-for="(obj, index) in results"
                :key="index"
                class="text-center"
              >
                <td width="5%">
                  {{ $utils.formatArrayValue(obj._source['基-経典番号']) }}
                </td>
                <td width="2%">
                  {{ $utils.formatArrayValue(obj._source['基-枝番']) }}
                </td>
                <td width="10%" class="pl-1 text-left">
                  <a
                    :href="
                      'https://21dzk.l.u-tokyo.ac.jp/SAT2018/' +
                      $utils.formatArrayValue(obj._source['sat_id']) +
                      '.html'
                    "
                    target="_blank"
                    >{{ $utils.formatArrayValue(obj._source['基-経典名']) }}</a
                  >
                </td>
                <td width="2%">
                  {{ $utils.formatArrayValue(obj._source['基-収録巻次']) }}
                </td>
                <td width="5%" class="pl-1 text-left">
                  {{ $utils.formatArrayValue(obj._source['基-部門']) }}
                </td>
                <td width="2%">
                  {{ $utils.formatArrayValue(obj._source['基-配本']) }}
                </td>
                <td width="5%">
                  {{ $utils.formatArrayValue(obj._source['基-年月日']) }}
                </td>
                <td
                  width="5%"
                  :bgcolor="
                    $utils.formatArrayValue(obj._source['勘-底本/校本']) ==
                    '底本'
                      ? '#BBDEFB'
                      : $utils.formatArrayValue(obj._source['勘-底本/校本']) ==
                        '校本'
                      ? '#FFCDD2'
                      : ''
                  "
                >
                  {{ $utils.formatArrayValue(obj._source['勘-底本/校本']) }}
                </td>
                <td width="10%" class="pl-1 text-left">
                  {{ $utils.formatArrayValue(obj._source['勘-❹']) }}
                </td>
                <td width="10%" class="pl-1 text-left">
                  {{ $utils.formatArrayValue(obj._source['勘-❼']) }}
                </td>
                <td width="10%" class="pl-1 text-left">
                  {{ $utils.formatArrayValue(obj._source['勘-❼備考']) }}
                </td>

                <td
                  width="5%"
                  :bgcolor="
                    $utils.formatArrayValue(obj._source['脚-底本/校本']) ==
                    '底本'
                      ? '#BBDEFB'
                      : $utils.formatArrayValue(obj._source['脚-底本/校本']) ==
                        '校本'
                      ? '#FFCDD2'
                      : ''
                  "
                >
                  {{ $utils.formatArrayValue(obj._source['脚-底本/校本']) }}
                </td>
                <td width="5%" class="pl-1 text-left">
                  {{ $utils.formatArrayValue(obj._source['脚-新添部分']) }}
                </td>
                <td width="10%" class="pl-1 text-left">
                  <a
                    :href="
                      'http://www.kanzaki.com/works/2016/pub/image-annotator?u=https://d1av1vcgsldque.cloudfront.net/iiif/' +
                      (
                        '0000' + $utils.formatArrayValue(obj._source['No.'])
                      ).slice(-4) +
                      '/manifest.json'
                    "
                    target="_blank"
                    >{{
                      $utils.formatArrayValue(obj._source['脚-テキスト'])
                    }}</a
                  >
                </td>
                <td width="10%" class="pl-1 text-left">
                  {{ $utils.formatArrayValue(obj._source['脚-備考']) }}
                </td>

                <td width="5%">
                  <nuxt-link
                    target="_blank"
                    :to="
                      localePath({
                        name: 'item-id',
                        params: {
                          id: (
                            '00000' +
                            $utils.formatArrayValue(obj._source['No.'])
                          ).slice(-5),
                        },
                      })
                    "
                    >{{ 'more' }}
                  </nuxt-link>
                </td>
              </tr>
            </table>

            <div class="text-center">
              <v-pagination
                v-model="currentPage"
                :length="paginationLength"
                :total-visible="7"
                @input="setCurrentPage"
              ></v-pagination>
            </div>
          </template>
        </v-col>

        <v-col v-show="facetFlag" :sm="4" order-sm="1">
          <template v-if="total > 0">
            <h3 class="mb-5">
              <v-tooltip bottom>
                <template v-slot:activator="{ on }">
                  <i
                    v-show="facetFlag"
                    style="cursor: pointer;"
                    class="far fa-caret-square-left mr-2"
                    v-on="on"
                    @click="facetFlag = !facetFlag"
                  ></i>
                </template>
                <span>{{ $t('close_facets') }}</span>
              </v-tooltip>
              {{ $t('refine_your_search') }}
            </h3>

            <FacetSearchOptions />
          </template>
        </v-col>
      </v-row>
    </v-container>
  </div>
</template>

<script lang="ts">
import { Vue, Component } from 'nuxt-property-decorator'
import SearchForm from '~/components/search/SearchForm.vue'
import SearchFilter from '~/components/search/filter.vue'
import FacetSearchOptions from '~/components/search/FacetSearchOptions.vue'

@Component({
  components: {
    SearchForm,
    SearchFilter,
    FacetSearchOptions,
  },
  watchQuery: true,
})
export default class search extends Vue {
  loadingFlag: boolean = false

  // ソート項目
  sortLabelsTmp: any = process.env.SORT_LABELS
  itemsSort: string[] = JSON.parse(this.sortLabelsTmp)

  get results() {
    return this.$store.state.result.hits.hits
  }

  get currentPage() {
    return this.$store.state.currentPage
  }

  set currentPage(value) {
    this.$store.commit('setCurrentPage', value)
  }

  get size() {
    return this.$store.state.size
  }

  set size(value) {
    this.$store.commit('setSize', value)
  }

  get sort() {
    return this.$store.state.sort
  }

  set sort(value) {
    this.$store.commit('setSort', value)
  }

  get facetFlag() {
    return this.$store.state.facetFlag
  }

  set facetFlag(value) {
    this.$store.commit('setFacetFlag', value)
  }

  async fetch(context: any) {
    const store = context.store
    const state = store.state

    if (state.index == null) {
      const uri = process.env.BASE_URL + '/index.json'
      const index = await context.app.$searchUtils.createIndexFromArray(uri)
      store.commit('setIndex', index.index)
      store.commit('setData', index.data)
    }

    if (Object.keys(state.facetLabels)) {
      // ファセット項目
      const facetLabels: any = process.env.FACETS_LABELS
      store.commit('setFacetLabels', JSON.parse(facetLabels))
      const facetFlags: any = process.env.FACETS_FLAGS
      store.commit('setFacetFlags', JSON.parse(facetFlags))
    }

    const routeQuery = context.query

    const esQuery = context.app.$searchUtils.createQuery(routeQuery, state)
    store.commit('setQuery', esQuery)

    const result = context.app.$searchUtils.search(
      store.state.index,
      store.state.data,
      store.state.query
    )

    console.log({ result, esQuery })

    context.store.commit('setResult', result)

    // --------

    store.commit('init')

    const keywords: any = routeQuery.keyword
    if (keywords) {
      store.commit('setKeyword', keywords)
    }

    for (const key in routeQuery) {
      if (key.includes('fc-')) {
        store.commit('setFc', {
          label: key,
          values: routeQuery[key],
        })
      }
    }

    const sort: any = routeQuery.sort
    if (sort) {
      store.commit('setSort', sort)
    }

    const from: any = routeQuery.from
    if (from) {
      store.commit('setFrom', Number(from))
    }

    const size: any = routeQuery.size
    if (size) {
      store.commit('setSize', Number(size))
    }

    const currentPage = state.from / state.size + 1
    store.commit('setCurrentPage', currentPage)

    if (process.browser) {
      window.scrollTo(0, 0)
    }
  }

  setCurrentPage() {
    if (this.currentPage > 0) {
      const query: any = Object.assign({}, this.$route.query)
      query.from = (this.currentPage - 1) * this.size
      this.updateQuery(query)
    }
  }

  setSize() {
    const query: any = Object.assign({}, this.$route.query)
    query.from = 0
    query.size = this.size
    this.updateQuery(query)
  }

  setSort() {
    const query: any = Object.assign({}, this.$route.query)
    query.from = 0
    query.sort = this.sort
    this.updateQuery(query)
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

  get total(): number {
    const result = this.$store.state.result
    if (result.hits) {
      return result.hits.total.value
    } else {
      return 0
    }
  }

  get paginationLength() {
    return Math.ceil(this.total / this.size)
  }

  get computedItemsSort() {
    const arr: any[] = [
      /*
      {
        value: '_score:desc',
        text: this.$t('relevance'),
      },
      */
    ]

    console.log(this.sort)

    const orders = ['asc', 'desc']

    const itemsSort = this.itemsSort

    for (let i = 0; i < itemsSort.length; i++) {
      const value = itemsSort[i]
      for (let j = 0; j < orders.length; j++) {
        const order = orders[j]
        const label = value.startsWith('_') ? this.$t(value.slice(1)) : value
        arr.push({
          value: value + '.keyword:' + order,
          text: label + ' ' + this.$t(order),
        })
      }

      if (i === 0 && this.sort == null) {
        this.sort = value + '.keyword:asc'
      }
    }

    return arr
  }
}
</script>
