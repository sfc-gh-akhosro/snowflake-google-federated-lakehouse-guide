

digraph isolated_iceberg {
    rankdir=TD
    graph [fontname="Helvetica", bgcolor="transparent", pad=0.4 fontsize=11 penwidth=0.2]
    node  [fontname="Helvetica", fontsize=10, style="filled,rounded", shape=box,
           fillcolor="#BBDEFB", color="#1565C0"]
    edge  [fontname="Helvetica", fontsize=9, color="#555555", arrowsize=0.7]

    subgraph cluster_snowflake {
        label=Snowflake
        team_sf [label="Finance Analyst"]
        sf [shape=record label="Snowflake Horizon"]
    }
    
    subgraph cluster_gcp {
        label = BigQuery
        team_bq [label="Marketing Analyst"]

        bq [label="Runtime Catalog"]
    }

    sf -> team_sf
    bq -> team_bq
    
    subgraph cluster_customer {
        graph[ labelloc=b]
        label="Customer Iceberg Buckets"

        finance_bucket [shape=record label="Finance\nBucket | {mortgage_rates | ...}"]
        marketing_bucket [shape=record label="Marketing\nBucket | {housing | ...}"]
    }
    
    team_sf -> finance_bucket
    team_bq -> marketing_bucket
}



digraph catalog_federation {
    rankdir=TD
    nodesep=1
    splines=true;
    
    graph [fontname="Helvetica", bgcolor="transparent", pad=0.4 style=dashed fontsize=14]
    node  [fontname="Helvetica", fontsize=12, style="filled,rounded", shape=box,
           fillcolor="#BBDEFB", color="#1565C0"]
    edge  [fontname="Helvetica", fontsize=10, color="#555555", arrowsize=0.7]
    
    

    // Snowflake side
    subgraph cluster_snowflake {
        label="Snowflake"

        HZ [label="Horizon Catalog"]
        SF [label="Finace Team"]
    }

    // GCP side
    subgraph cluster_gcp {
        label="GCP Lakehouse"

        BLM [label="Runtime Catalog"]
        BQ  [label="Marketing Team"]
    }
    

    gcs [label= "{Customer GCS | {Finance Bucket | Marketing Bucket}}" shape=record]
    

    // Engines read/write to storage
    SF -> gcs [style=dashed, label="Read/Write"]
    BQ -> gcs [style=dashed, label="Read/Write"]
    
    HZ -> BLM [headlabel="Snowflake CLD" constraint=false labeldistance=8 labelangle=5]
    BLM -> HZ [label="GCP External Catalog" constraint=false]
    
    HZ -> SF
    BLM -> BQ
}

