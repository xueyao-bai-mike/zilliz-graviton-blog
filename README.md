# Achieve Better Price to Performance up to 41% for Zilliz on Amazon with Graviton3 Processors

<picture>
 <img alt="YOUR-ALT-TEXT" src="assets/zilliz-brand.png">
</picture>

<sub> _Guest post by Xueyao Bai & Quan Yuan & Wantao Wu, Solution Architect@AWS, Yusheng Ma@Zilliz_ <sub>

The popularity of generative AI (Generative AI) has aroused widespread attention and completely ignited the vector database market. 

According to IDC's forecast, by 2025, more than 80% of business data will be unstructured, stored in text, image, audio, video or other formats. However, storing and querying unstructured data on a large scale is a very big challenge.

The common practice in the field of generative AI and deep learning is to store unstructured data by converting it into vectors, and to search for semantic relevance through vector similarity search (Vector similarity search) technology. Fast storage, indexing and searching of Embedding vectors are the core functions of vector databases.

## About Zilliz

Zilliz is built by the engineers and scientists who created LF AI Milvus®, an open-source vector database for production ready AI. On a mission to unleash data insights with AI, the company builds the database and search technologies to help organizations create AI/ML applications stunningly easy and fast.

## About Amazon Graviton
AWS Graviton processors are designed by AWS to deliver the best price performance for your cloud workloads running in Amazon EC2.

AWS Graviton3 processors are the latest in the AWS Graviton processor family. They provide up to 25% better compute performance, up to 2x higher floating-point performance, and up to 2x faster cryptographic workload performance compared to AWS Graviton2 processors. AWS Graviton3 processors deliver up to 3x better performance compared to AWS Graviton2 processors for ML workloads, including support for bfloat16. They also support DDR5 memory that provides 50% more memory bandwidth compared to DDR4. AWS Graviton3E processors deliver up to 35% higher vector-instruction performance compared to AWS Graviton3 processors. This improvement provides higher performance benefits for HPC applications.

## Benchmark Description&Goal

To better understand what Graviton3 can bring to Zilliz, we will perform load test against Intel Xeon Platinum 8375C. 
Specific details is shown in below table:

<picture>
 <img alt="YOUR-ALT-TEXT" src="assets/ec2-type.png">
</picture>

<sub> _Graviton3 (m7g) VS Intel Xeon Platinum 8375C (m6i)_ <sub>
* OS and Storages:
    * AMI：Amazon Linux 2023 AMI(latest version)
    * EBS：gp3, 80G(keep default performance level, Throughput 125MB/s, IOPS 3000)


### Zilliz Benchmark Tools & Dataset Introduction

We are using VectorDBBench to perform test. VectorDBBench is a go-to tool for the ultimate performance and cost-effectiveness comparison. Designed with ease-of-use in mind, VectorDBBench is devised to help users, even non-professionals, reproduce results or test new systems, making the hunt for the optimal choice amongst a plethora of cloud services and open-source vector databases a breeze.

We are using Cohere https://huggingface.co/datasets/Cohere/wikipedia-22-12/tree/main/en as testing dataset.


### Methodology

请袁泉老师参考下tidb这个综述实验环境设置的部分，也综述下咱们的情况

Our test used two industry standard OLTP benchmarks: TPC-C and sysbench. TPC-C tests the OLTP system by using a commodity sales model that involves five different transaction types. TPC-C generally works for any database which handles OLTP workloads. Sysbench is a well-established tool that runs synthetic benchmarks of MySQL and the hardware it runs on. It also has an option to execute OLTP workloads on a MySQL database. Since TiDB is MySQL compatible, sysbench will be a good reference as well.


### Milvus Installation

* Logon SUT EC2 instance above with SSH session separately.
* Perform following commands to setup Milvus 2.3 benchmark environment:

```
sudo su - root

## Install Docker and docker-compose
dnf install -y docker git htop
systemctl start docker
ARCH=$(arch)
curl -SL https://github.com/docker/compose/releases/download/v2.12.2/docker-compose-linux-${ARCH} \
     -o ./docker-compose
chmod +x docker-compose
mv docker-compose /usr/bin/

## Start milvus container
mkdir ~/milvus
cd ~/milvus
wget https://github.com/milvus-io/milvus/releases/download/v2.3.0/milvus-standalone-docker-compose.yml \
  -O docker-compose.yml
docker-compose up -d
docker-compose ps

```

After above steps, you should get Milvus v2.3.0 running on both instances. 
### VectorDBBench installation

We will install VectorDBBench on LoadGen instance, follow below steps to complete.

```
sudo su - root

## Need isntall Python 3.11 or above
dnf install -y python3.11 python3.11-pip python3.11-devel git gcc gcc-c++
python3.11 -V

## Install VectorDBBench
pip3.11 install vectordb-bench
which init_bench

## Redirect /tmp/vectordb_bench to another location
mkdir -p /mnt/vectordb_bench
ln -s /mnt/vectordb_bench /tmp/vectordb_bench

## init benchmark tool，follow the prompt to enter web page
init_bench
```
<picture>
 <img alt="YOUR-ALT-TEXT" src="assets/benchmark_install.png">
</picture>

Visit VectorDBBench Web Console via External URL, make sure you have allowed port 8501 in security group and allow the traffic in your VPC.

<picture>
 <img alt="YOUR-ALT-TEXT" src="assets/benchmark_webui.png">
</picture>

### Peform loadtest

* Press 【Run Your Test >】button to enter benchmark configurations, follow the instruction shown below to configure the benchmark tool.

<picture>
 <img alt="YOUR-ALT-TEXT" src="assets/run_benchmark1.png">
</picture>

<picture>
 <img alt="YOUR-ALT-TEXT" src="assets/run_benchmark2.png">
</picture>

<picture>
 <img alt="YOUR-ALT-TEXT" src="assets/run_benchmark3.png">
</picture>


### Explore Result 

* After all bechmark tests complete, you can find result via SSH session

<picture>
 <img alt="YOUR-ALT-TEXT" src="assets/result.png">
</picture>

### Performance Comparison between Intel & Graviton3 instances

After above test running against m6i (Intel) m7g(Graviton3), we have final result as below:

<picture>
 <img alt="YOUR-ALT-TEXT" src="assets/compare.png">
</picture>

## Summary

Our test shows that general performance of Milvus&Zilliz Cloud on Graviton3 (m7g) achieve better QPS than Intel Xeon Platinum 8375C (m6i). We can also notice Zilliz Cloud achieve 5x better performance than latest Milvus v2.3. This echos Zilliz's [leaderboard]( https://zilliz.com/vector-database-benchmark-tool)

We encourage readers to try Graviton3 whenever possible and feel the pricing to performance improvement in Graviton3.

## Reference
* VDBBench https://github.com/zilliztech/VectorDBBench
* Cohere https://huggingface.co/datasets/Cohere/wikipedia-22-12/tree/main/en


