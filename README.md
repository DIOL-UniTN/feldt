# Interpretable Federated Reinforcement Learning for Large-Scale Distributed HVAC Control

**Code repository for the paper**  
**_Interpretable Federated Reinforcement Learning for Large-Scale Distributed HVAC Control_**  
Published at **EvoStar 2026**

## Contributions

Authors:
- Stefano Genetti, stefano.genetti@unitn.it
- Enrico Micheli, michelienrico@hotmail.it
- Leonardo Lucio Custode, leonardo.custode@gmail.com
- Giovanni Iacca, giovanni.iacca@unitn.it

For every type of doubt/question about the repository please do not hesitate to contact us.

---

## Repository Overview

The framework implements a **federated evolutionary reinforcement learning** setup for distributed HVAC control:

- Each **client** represents a building and locally optimizes an interpretable HVAC control policy.
- Policies are represented as **Decision Trees**, evolved using **Grammatical Evolution** and refined via **Q-learning**.
- A **federated server** maintains a *Hall of Fame* (HoF) of high-performing policies and redistributes them to clients across FL rounds.

---

## Usage

### Server

Build the federated server:

```bash
sudo docker build -f Dockerfile_server -t custom-flower-server .
```

Run the server:

```bash
sudo docker run -it --name flower-server --rm --network host -p 8080:8080 \
custom-flower-server \
--address flower-server:8080 \
--rounds 20 \
--min_fit_clients 1 \
--min_eval_clients 1 \
--min_available_clients 1
```

### Client

Build the client image:

```bash
sudo docker build -f Dockerfile_client -t sinergym-flower-client .
```

Run a single client:

```bash
sudo docker run -v fl_exp:/c1_data -it --rm --network host \
sinergym-flower-client \
--environment_name Eplus-5zone-mixed-discrete-stochastic-v1 \
--jobs 10 \
--n_actions 10 \
--learning_rate 0.001 \
--df 0.05 \
--input_space 11 \
--episodes 10 \
--episode_len 822 \
--lambda_ 10 \
--generations 5 \
--cxp 0.2 \
--mp 1 \
--low -1 \
--up 1 \
--types '#1,13,1,1;0,24,1,1;-10,50,1,1;0,100,1,1;0,300,1,1;0,1000,1,1;-10,50,1,1;-10,50,1,1;-10,50,1,1;0,100,1,1;0,50,1,1' \
--mutation 'function-tools.mutUniformInt#low-0#up-40000#indpb-0.1' \
--patience 30 \
--timeout 600 \
--grammar_angle 1 \
--seed 1 \
--clients=1
```

### Multiple Clients

To simulate multiple buildings, launch multiple client containers independently.

**Important**
Do not rely on the ```--clients``` parameter for parallel clients. Due to known gRPC issues, this may cause instability.

Instead, use:

```bash
./launch_clients.sh
```

or manually start multiple client containers.
