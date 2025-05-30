<template>
  <div class="rank-card">
    <van-cell class="cell" :border="false" is-link @click="toDiscovery">
      <template #title>
        <Icon class="icon discovery-icon" name="discovery" />
        <span class="title">{{ $t('common.discovery') }}</span>
      </template>
    </van-cell>
    <div class="card-box">
      <swiper class="swipe-wrap" :options="swiperOption">
        <swiper-slide v-for="art in artList.slice(0, 10)" :key="art.id" class="swipe-item">
          <ImageCard mode="meta" square :artwork="art" @click-card="toArtwork(art)" />
        </swiper-slide>
        <swiper-slide class="swipe-item more">
          <ImageSlide :images="slides">
            <div class="link" @click="toDiscovery">
              <van-loading v-if="loading" class="d-loading" :size="'50px'" />
              <template v-else>
                <Icon name="more" scale="20" />
                <div>{{ $t('common.view_more') }}</div>
              </template>
            </div>
          </ImageSlide>
        </swiper-slide>
        <div slot="scrollbar" class="swiper-scrollbar"></div>
        <div slot="button-prev" class="swiper-button-prev"></div>
        <div slot="button-next" class="swiper-button-next"></div>
      </swiper>
    </div>
  </div>
</template>

<script>
import _ from '@/lib/lodash'
import api from '@/api'
import { SessionStorage } from '@/utils/storage'
import ImageCard from '@/components/ImageCard'
import ImageSlide from '@/components/ImageSlide'

export default {
  name: 'DiscoveryCard',
  components: {
    ImageCard,
    ImageSlide,
  },
  data() {
    return {
      artList: [],
      loading: true,
      swiperOption: {
        freeMode: true,
        slidesPerView: 'auto',
        // mousewheel: true,
        scrollbar: {
          el: '.swiper-scrollbar',
          draggable: true,
        },
        navigation: {
          nextEl: '.swiper-button-next',
          prevEl: '.swiper-button-prev',
        },
      },
    }
  },
  computed: {
    slides() {
      const artList = this.artList.slice(10, 15)
      return artList.map(art => {
        return {
          title: art.title,
          src: art.images[0].m,
        }
      })
    },
  },
  mounted() {
    this.getRankList()
  },
  methods: {
    async getRankList() {
      this.loading = true
      const res = await api.getDiscoveryList('safe', 18)
      if (res.status === 0) {
        this.artList = _.shuffle(res.data)
      } else {
        this.$toast({
          message: res.msg,
          icon: require('@/icons/error.svg'),
        })
      }
      this.loading = false
    },
    toDiscovery() {
      SessionStorage.set('discovery.illusts', this.artList)
      this.$router.push({
        name: 'Discovery',
      })
    },
    toArtwork(art) {
      this.$store.dispatch('setGalleryList', this.artList)
      this.$router.push({
        name: 'Artwork',
        params: { id: art.id, art },
      })
    },
  },
}
</script>

<style lang="stylus" scoped>
.rank-card {
  padding: 0 14px;
  margin-bottom: 24px;

  .discovery-icon {
    margin-right 0px !important
  }

  .d-loading {
    position absolute
    top 50%
    left 50%
    transform translate(-50%, -50%) !important
  }

  .card-box {
    // padding: 0 12px;
    height: 365px;

    .swipe-wrap {
      height: 100%;
      // border-radius: 20px;
      overflow: hidden;

      .swipe-item {
        width 4.4rem
        margin-right: 12px;

        @media screen and (max-width: 500px) {
          width 4.65rem
        }

        &:last-child {
          .image-card {
            margin-right: 0;
          }
        }

        .image-card {
          // width: 50vw;
          font-size: 0;
          border: 1PX solid #ebebeb;
          box-sizing: border-box;
          width: 100%;
          height: 97%;
          padding-bottom: 0 !important;
        }

        .image-slide {
          border: 1PX solid #ebebeb;
          border-radius: 18px;
          box-sizing: border-box;
          height: 97%;

          .link {
            position: absolute;
            width: 100%;
            height: 100%;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            color: #efefef;

            &::before {
              content: '';
              position: absolute;
              top: 0;
              left: 0;
              right: 0;
              bottom: 0;
              background: rgba(#000, 0.6);
            }

            svg {
              position: absolute;
              top: 50%;
              left: 50%;
              transform: translate(-50%, -55%);
              font-size: 20em;
            }

            div {
              position: absolute;
              left: 50%;
              top: 50%;
              transform: translate(-50%, 80%);
              font-size: 34px;
              text-align: center;
              white-space: nowrap;
            }
          }
        }

        &.more {
          .rank {
            display: flex;
            height: 100%;
            justify-content: center;
            align-items: center;
          }
        }
      }
    }
  }
}
</style>
