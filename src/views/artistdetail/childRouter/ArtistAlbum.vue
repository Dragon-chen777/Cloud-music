<template>
  <div class="artist-album" v-show="artist != null">
    <artist-hot50 :musiclist="musiclist" />
    <artist-album-list
      v-for="(item, index) in albumlist"
      :key="index"
      :album="item"
    />
  </div>
</template>

<script>
import ArtistHot50 from "../childComps/ArtistHot50";
import ArtistAlbumList from "../childComps/ArtistAlbumList";

import { _getArtistHot50, _getArtistAlbum } from "network/artist";
import { _getSongsDetail, songDetail } from "network/detail";

export default {
  name: "ArtistAlbum",
  components: {
    ArtistHot50,
    ArtistAlbumList,
  },
  data() {
    return {
      aitist: null,
      musiclist: [],
      albumlist: [],
    };
  },
  created() {
    this.artist = this.$route.query.artist || this.$store.state.artist;
    if (this.artist != null) {
      //获取前五十首热歌
      _getArtistHot50(this.artist.id).then((res) => {
        let songs = res.data.songs;
        for (let i in songs) {
          _getSongsDetail(songs[i].id).then((res) => {
            let song = new songDetail(res.data.songs);
            this.musiclist.push(song);
          });
        }
      });
      // 获取专辑
      _getArtistAlbum(this.artist.id).then(res=>{
          this.albumlist = res.data.hotAlbums;
      })
    }
  },
};
</script>

<style scoped>
.artist-album{
    width: 100%;
}
</style>