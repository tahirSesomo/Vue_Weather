<template>
  <div class="flex py-6 px-3 bg-weather-secondary rounded-md shadow-md">
    <div class="flex flex-col flex-1 gap-2">
      <AnimatedSkeleton class="max-w-[50%]"/>
      <AnimatedSkeleton class="max-w-[40%]" />
    </div>
    <div class="flex flex-col items-end flex-1 gap-2">
      <AnimatedSkeleton class="max-w-[50px] w-full"/>
      <AnimatedSkeleton class="max-w-[75px] w-full" />
    </div>
  </div>
</template>

<script>
import AnimatedSkeleton from "@/components/AnimatedSkeleton";
export default {
  name: "CityCardSkeleton",
  components: {AnimatedSkeleton}
}
</script>
