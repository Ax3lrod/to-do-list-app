<template>
  <div class="relative">
    <button>
      <NuxtImg
        src="/listsymbol.svg"
        height="35"
        width="35"
        class="fixed left-1/2 transform -translate-x-[150px] translate-y-[30px] md:-translate-x-[300px]"
      />
    </button>
    <button>
      <NuxtImg
        src="/calendarsymbol.svg"
        height="40"
        width="40"
        class="fixed left-1/2 transform translate-x-[125px] translate-y-[25px] md:translate-x-[275px]"
      />
    </button>
    <div
      class="w-screen bg-teal1 h-[90px] rounded-t-3xl lg:hidden bottom-0 z-0"
    />
  </div>
  <button>
    <NuxtImg
      src="/addbutton2.svg"
      width="75"
      height="75"
      class="lg:hidden fixed left-1/2 transform -translate-x-1/2 bottom-[50px]"
    />
  </button>
</template>
