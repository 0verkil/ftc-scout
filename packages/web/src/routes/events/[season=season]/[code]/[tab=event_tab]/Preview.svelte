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
    //$: totalPoints = descriptor.pensSubtract || remote ? "totalPoints" : "totalPointsNp";
    $: defaultStats = ["team", "totalPointsNpOpr"]; //[...(remote ? [] : [totalPoints + "Opr"])];

    //$: console.log(data);

    $: data = teamData.map(chooseLastApplicableTep).filter((team: any) => !!team);

    function chooseLastApplicableTep(individualTeamData: any[]) {
        let bestTepCandidate = null;
        //individualTeamData.reverse();
        // console.log(individualTeamData[0])
        individualTeamData.sort(
            (
                tep: { event: { start: string | number | Date } },
                other: { event: { start: string | number | Date } }
            ) => new Date(tep.event.start).valueOf() - new Date(other.event.start).valueOf()
        );
        // console.log(individualTeamData[0])
        for (let tep of individualTeamData) {
            if (!!tep?.stats) {
                console.log("new best for " + tep.team.number + " at date " + tep.event.end);
                bestTepCandidate = tep;
            } else {
                continue;
            }
        }
        return bestTepCandidate;
    }

    $: saveId = `eventPageTep${season}${remote ? "Remote" : "Trad"}`;

    $: underscoreEventName = eventName.replace(" ", "_");
    $: filename = `${season}_${underscoreEventName}_Team_Stats`;
    $: title = `${season} ${eventName} Team Stats`;
    $: csv = { filename, title };
</script>

{#if data.length > 0}
    <LocalStatTableControls
        {saveId}
        {data}
        {focusedTeam}
        {stats}
        {defaultStats}
        defaultSort={{ id: "totalPointsNpOpr", dir: SortDir.Asc }}
        hideRankStats={["eventRank", "rankingPoints"]}
        {csv}
    />
{:else}
    <p>unfortunately the data is still loading</p>
{/if}
