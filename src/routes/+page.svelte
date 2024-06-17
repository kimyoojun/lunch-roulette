<script lang="ts">
    let lunchMenu = ["돼지고기", "소고기", "양고기", "흑돼지고기", "오리고기", "치킨"]

    let lunch = lunchMenu[0]

    const array_chunk = (arr: string[], chunk_size: number) => {
        let chunked_array = [];
        for (let i = 0; i < arr.length; i += chunk_size) {
            chunked_array.push(arr.slice(i, i + chunk_size));
        }
        return chunked_array;
    }

    let lunchMenuChunked = array_chunk(lunchMenu, 3)

    const delay = (ms: number) => new Promise(res => setTimeout(res, ms))

    const roulette = async () => {
        for (let i = 0; i < 30; i++) {
            let randomMenu = Math.floor(Math.random() * lunchMenu.length)
            lunch = lunchMenu[randomMenu]
            await delay(80)
        }

    }
</script>

<div class="size-full flex flex-col justify-start items-center gap-y-3">
    <h1 class="text-3xl font-black">오늘 뭐묵?</h1>
    <p>무조건 먹어야함</p>
    <div class="card flex items-center shadow-xl border bg-zinc-100 p-8 gap-y-3">
        <div class="w-full flex justify-start text-xl">배달 메뉴</div>
        {#each lunchMenuChunked as lunchRow}
            <div class="flex gap-x-5 w-full">
                {#each lunchRow as Menu}
                    <div class="card shadow-xl bg-zinc-300 w-64 p-3 flex justify-center items-center mt-3">{Menu}</div>
                {/each}
            </div>
        {/each}
    </div>
    <h2 class="mt-10 text-lg">과연 오늘의 점심 메뉴는?</h2>
    <div class="card shadow-xl w-[30rem] flex justify-center items-center p-5 text-2xl bg-zinc-100">{lunch}</div>
    <button class="btn btn-outline w-[30rem] mt-3" on:click={roulette}>점심 메뉴 고르기</button>
</div>
