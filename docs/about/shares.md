# Contributing Member Shares

## Compute Shares

<script src="https://d3js.org/d3.v3.min.js"><!-- ~150 kB --></script>
<script src="https://cdn.datatables.net/1.10.16/js/jquery.dataTables.min.js"><!-- ~80 kB --></script>
<script src="https://cdn.datatables.net/1.10.16/js/dataTables.bootstrap.min.js"><!-- 2 kB --></script>

<table id="hosttable">
</table>

<script type="text/javascript" charset="utf-8">
d3.text("{{ '/assets/data/compute_shares.tsv' | relative_url }}", "text/csv", function(host_table) {
  // extract date from header comments
  var timestamp = host_table.match(/^[#] Created on: [^\r\n]*[\r\n]+/mg, '')[0];
  timestamp = timestamp.replace(/^[#] Created on: /g, '');
  timestamp = timestamp.replace(/ [^ ]+/g, ''); // keep only the date
  timestamp = timestamp.trim();
  d3.select("#compute-shares-timestamp").text(timestamp);
  
  // drop header comments
  host_table = host_table.replace(/^[#][^\r\n]*[\r\n]+/mg, '');
  host_table = d3.tsv.parse(host_table);

  var table = d3.select("#hosttable");
  var thead, tbody, tfoot, tr, td, td_status;
  var value, value2;
  var funits = 0, slots = 0, project = "";
  
  /* For each row */
  var nodes = 0;
  host_table.forEach(function(row0) {
    /* Ignore column on /tmp size, iff it exists */
    var row = [row0["funits"], row0["queue_slots"], row0["project"]];

    if (nodes == 0) {
      tr = table.append("thead").append("tr");
      tr.append("th").text("Functional Shares");
      tr.append("th").text("Slots (member.q)");
      tr.append("th").text("Group");
      tbody = table.append("tbody");
    }
    
    tr = tbody.append("tr");
    for (key in row) td = tr.append("td").text(row[key]);
      
    nodes += 1;
  });
  
  $(document).ready(function() {
    $('#hosttable').DataTable({
      "pageLength": 50,
      "order": [[ 0, "desc" ]]
    });
  });
});
</script>

Source: [compute_shares.tsv]({{ '/assets/data/compute_shares.tsv' | relative_url }}) produced on <span id="compute-shares-timestamp"></span> (compiled from `qconf -srqs member_queue_limits` and `qconf -sprj <project>`)


<style>
table {
  margin-top: 2ex;
  margin-bottom: 2ex;
}
tfoot {
  border-top: 2px solid #000;
  font-weight: bold;
}
ttr:last-child { border-top: 2px solid #000; }
</style>