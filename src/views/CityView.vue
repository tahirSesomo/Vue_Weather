<template>
  <div>
    <Suspense>
      <AsyncCityView />
      <template #fallback>
        <CityViewSkeleton />
      </template>
    </Suspense>
  </div>
</template>

<script>
import AsyncCityView from "@/components/AsyncCityView.vue";
import CityViewSkeleton from "@/components/CityViewSkeleton";
export default {
  name: "CityView",
  components: {CityViewSkeleton, AsyncCityView}
}
</script>
