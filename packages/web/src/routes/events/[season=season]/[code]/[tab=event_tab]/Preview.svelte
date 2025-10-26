<script lang="ts">
    import { DESCRIPTORS, getTepStatSet, SortDir, type Season } from "@ftc-scout/common";
    import LocalStatTableControls from "$lib/components/stats/LocalStatTableControls.svelte";

    export let season: Season;
    export let remote: boolean;
    export let eventName: string;
    export let teamData: any;
    export let focusedTeam: number | null;

    $: descriptor = DESCRIPTORS[season];
    $: stats = getTepStatSet(season, remote);
    $: totalPoints = descriptor.pensSubtract || remote ? "totalPoints" : "totalPointsNp";
    $: defaultStats = [...(remote ? [] : [totalPoints + "Opr"])];

    $: console.log(data);

    $: data = teamData.filter((team: { stats: any }) => {
        return !!team?.stats;
    });

    $: saveId = `eventPageTep${season}${remote ? "Remote" : "Trad"}`;

    $: underscoreEventName = eventName.replace(" ", "_");
    $: filename = `${season}_${underscoreEventName}_Team_Stats`;
    $: title = `${season} ${eventName} Team Stats`;
    $: csv = { filename, title };
</script>

<LocalStatTableControls
    {saveId}
    {data}
    {focusedTeam}
    {stats}
    {defaultStats}
    defaultSort={{ id: "eventRank", dir: SortDir.Asc }}
    hideRankStats={[
        "eventRank",
        "rankingPoints",
        ...(descriptor.rankings.rp == "Record" ? ["record"] : ["totalPointsAvg", "totalPointsTot"]),
    ]}
    {csv}
/>
