
# librdkafka for OpenWrt

1. Add `src-git librdkafka https://github.com/ifding/openwrt-librdkafka.git` to your `feeds.conf`

2. Update the feed: `./scripts/feeds update librdkafka`

3. And then install it: `./scripts/feeds install librdkafa`

4. Now your package should be aviable when you do: `make menuconfig`
