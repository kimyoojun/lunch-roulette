<script lang="ts">
    import { supabase } from "$lib/Supabase"
    import { uuid } from "@supabase/supabase-js/dist/main/lib/helpers"
    import {onMount} from "svelte";

    // 메뉴 종류
    let lunchMenu: string [] = []
    // 선택된 메뉴
    let lunch = lunchMenu[0]
    // 추가할 메뉴
    let addMenu: string
    // 현재 로딩 상태
    let loading = true

    // 2차원 배열로 만들어주는 함수
    const array_chunk = (arr: string[], chunk_size: number) => {
        let chunked_array = [];
        for (let i = 0; i < arr.length; i += chunk_size) {
            chunked_array.push(arr.slice(i, i + chunk_size));
        }
        return chunked_array;
    }

    // 메뉴를 3개씩 묶은 이차원 배열
    let lunchMenuChunked = array_chunk(lunchMenu, 3)

    const delay = (ms: number) => new Promise(res => setTimeout(res, ms))

    // 배열에서 랜덤 데이터 선택하는 함수
    const roulette = async () => {
        for (let i = 0; i < 30; i++) {
            let randomMenu = Math.floor(Math.random() * lunchMenu.length)
            lunch = lunchMenu[randomMenu]
            await delay(80)
        }
    }

    // 추가 버튼 눌렀을때 실행되는 함수
    const menuAdd = async () => {
        const { error: addError } = await supabase
            .from("menu")
            .insert({
                id: uuid(),
                name: addMenu,
            })

        if (addError) return console.error(addError)
        addMenu = ""

        const { data, error} = await supabase
            .from("menu")
            .select("name")

        if (error) return console.error(error)

        lunchMenu = []
        data.forEach(menu => {
            lunchMenu.push(menu.name)
        })

        lunchMenuChunked = array_chunk(lunchMenu, 3)

        lunch = lunchMenu[0]
    }

    // 메뉴추가 input에서 엔터키 눌렀을때 실행되는 함수
    const keydown = (e: KeyboardEvent) => {
        if(e.key === "Enter") {
            menuAdd()
        }
    }

    onMount(async () => {
        loading = true
        const { data, error } = await supabase
            .from("menu")
            .select("name")

        if (error) return console.error(error)

        lunchMenu = []
        data.forEach(menu => {
            lunchMenu.push(menu.name)
        })

        lunchMenuChunked = array_chunk(lunchMenu, 3)

        lunch = lunchMenu[0]

        loading = false
    })
</script>

{#if loading}
    <div class="size-full skeleton"/>
{:else}
    <div class="size-full flex flex-col justify-start items-center gap-y-5">
        <h1 class="text-3xl font-black">오늘 뭐묵?</h1>
        <div class="card flex items-center shadow-xl border bg-zinc-100 p-8 gap-y-3">
            <div class="w-full flex justify-between items-center text-xl space-be space-x-reverse">
                <div>배달 메뉴</div>
                <div class="flex w-80 fustify-center items-center">
                    <input on:keydown={keydown} bind:value={addMenu} type="text" placeholder="메뉴 추가" class="input input-bordered w-full max-w-xs bg-zinc-50" />
                    <button class="btn bg-zinc-300 ml-1" on:click={menuAdd}>
                        추가
                    </button>
                </div>
            </div>
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
        <button class="btn btn-outline w-[30rem]" on:click={roulette}>점심 메뉴 고르기</button>
    </div>
{/if}
