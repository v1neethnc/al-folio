---
layout: page
title: Fail-Silent Replicated Token Manager with Atomic Semantics
description: Project to build a client-server token management system that simulates a fail-silent system.
importance: 2
img: assets/img/token_mgmt/server_65000_log.png
category: academic
---

**NOTE: This project was done over a duration of 7 weeks to fulfill the Course Project requirement for CMSC 621 - Advanced Operating Systems at University of Maryland, Baltimore County. The following is a report of the project, of which I am the sole contributor.**

Time Period: April, 2021 -- May, 2021
<br><br>

## Contents
-----
* [Introduction](#introduction)
* [Extension for this Project](#extension-for-this-project)
* [Implementation](#implementation)
  * [Create RPC Call](#create-rpc-call)
  * [Write RPC Call](#write-rpc-call)
  * [Read RPC Call](#read-rpc-call)
  * [Drop RPC Call](#drop-rpc-call)
  * [RIWMTest RPC Call](#riwmtest-rpc-call)
  * [Fail-Silent Behaviour](#fail-silent-behaviour)
  * [Token Information](#token-information)
  * [Logs](#logs)
  * [Other Functions](#other-functions)
* [Results](#results)
<br><br>

## Introduction
-----
This project is an extension of a smaller token management project done earlier in the semester for the same course. This section explains the basics outlined in that project.

The basic token management system involved a single server and multiple clients, where the server handles the token and the clients send RPC calls to the server, each of them corresponding to a different operation to be performed. In this project, a token is an abstract data type with the following properties: id, name, domain, and state. Every token has an unique identifier in the form of id, and a string of characters for the name property. The domain consists of three unsigned int64 numbers: `low, mid, high` such that `low <= mid <= high`. The state of the token consists of two integers: a partial value and a final value, which is defined at the integer `x` in the range `[low, mid)` and `[low, high)`, respectively, that minimizes `h(name, x)` for a hash function h. Hash function used in SHA-256.

The following operations are to be supported:
* **create(id):** create a token with the given id. Return a success or fail response.
* **drop(id):** to destroy/delete the token with the given id
* **write(id, name, low, high, mid):**
  * set the properties name, low, mid, and high for the token with the given id. Assume uint64 integers `low <= mid < high`.
  * compute the partial and final value of the token
  * Partial value is `min(H(name, x) for x in [low, mid))`
  * Final value is `min(Partial Value, min H(name, x) for x in [mid, high))`
  * Return the final state of the token consisting of partial value and final value on success or fail response otherwise
* **read(id):** returns token state value on success or fail response otherwise
<br><br>

## Extension for this Project
-----
This specific project is an extension of the above described project. The existing client-server token management system should be extended to accommodate token replication and atomic semantics. Each token in the system should be implemented as a (1,N) register with atomic semantics. Effectively, there is one system with the write permission for the token, and all the other systems can have read permissions. However, in this case, each token has a few more details. A YAML file accessible to all the servers contains information about all the tokens, containing each token's ID, address of the sole writer, and addresses of the readers. The assumption is that the replication schemes of all the tokens are static and are known beforehand to all the nodes. It is important to note that each node runs on localhost, on different ports.

The existing server API must be extended, with any additional RPC calls as needed, to support atomic semantics: for any given token, whenever a client read returns a value `v` and a subsequent (by any) client read returns a value `w`, then the write of `w` does not precede the write of `v`. To this end, there must be an implementation of the read-impose-write-majority (quorum) protocol.

Finally, fail-silent behaviour is to be emulated for a token. Consider a token M that exists in a quorum. A system may crash-stop for the token `M` after a time `t`, which means that the system may respond to other tokens that it is a writer/reader for, except for the specific token `M`.
<br><br>

## Implementation
-----
As specified, the project is completely written in Go, with support from YAML files to hold some necessary information, bash files containing multiple commands to execute the final project, and log files to track node activity.

### Create RPC Call
Upon encountering a Create flag for a specified token in the command line, the client program reads the `yaml_final.yml` file and fetches the corresponding token's data. Once the data is fetched, the writer of the token is spawned by the client, which then becomes the recipient of a subsequent Create RPC call.

The writer, upon receiving the Create RPC Call, creates the token if it does not already exist. It then takes the responsibility of spawning the reader servers and issuing Create RPC calls to them so that the token may be replicated. The RPC calls are modified to be flexible enough to determine where the source of the RPC call is, which in turn allows the servers to determine whether to fetch data from the YAML file or not.

### Write RPC Call
This works more or less on the same ideas behind the Create RPC call. Upon encountering the Write flag, the client program reads the `yaml_final.yml` file and fetches the corresponding token's data. Once the data is fetched, the writer of the token is sent the Write RPC call.

The writer, upon receiving the Write RPC call compares the timestamp on the token to the incoming call's timestamp. If the incoming call has a more recent timestamp, then the write operation is performed. The token's state information is updated, including the new timestamp. Now, it takes the responsibility of informing the readers to update the token's state information as well by sending the relevant readers the Write RPC call. As with Create, the Write RPC call is flexible enough to determine where the source of the call is, thereby allowing a distinction between the writer and the reader in the same function.

### Read RPC Call
Here, the client program reads the `yaml_final.yml` file and fetches the corresponding token's data. This time, it focuses on getting the list of readers and chooses a reader randomly to send the Read RPC call to. Once the corresponding reader receives the RPC call, it checks whether the token exists or not. After this check is passed, the Read-Impose Write-Majority comes into play, where all the nodes with the token are contacted by dispatching RPC calls as goroutines. This is where the **RIWMTest RPC Call** comes into play. 

When a server receives this call, it checks for the token for which the information is requested, and sends back the timestamp and the final value. The reader that invokes this RPC call then begins to collect the results of the goroutines. Through the process of collection, the timestamps are compared and the most recent value is calculated. However, the read waits only until a majority of the calls are executed and returned, and returns the updated information to the client while also writing back to the other nodes.

### Drop RPC Call
Like with the Create and Write RPC calls, the client program fetches the token's information from the `yaml_final.yml` file and gets the writer. The writer then receives a Drop RPC call that has to be executed. If the Drop RPC call is more recent than the last performed operation on the token, then the token is dropped from the server. 

The writer then dispatches Drop RPC calls to all the readers in order to ensure that the replicated token is deleted. It does this by dispatching the Drop RPC calls as goroutines, thereby ensuring that the deletion is not halted, and that the client would not have to wait for too long.

### RIWMTest RPC Call
This call is aimed at implementing the read-impose-write-majority quorum protocol. This call is the one that implements the fail-silent behavior by putting the routine to sleep if the fail-silent conditions are met.

### Fail-Silent Behaviour
To satisfy the requirement of emulating fail-silent behaviour, the server contains hardcoded values to check for the conditions for a specific token in a specific server. This is done so that pinpointing the behaviour will be simple. In this project, the fail-silent behaviour is emulated for the token with the ID: 1020 in the server running on the port: 65000. The server running on this port will fail to respond to queries on the token 1020 after a time duration of 10 seconds since the token's creation.

### Token Information
The information of each token is stored in the YAML file. As specified in the project requirement, each token's data in the YAML file consists of three parts: token ID, writer address, reader addresses. Each token has only one writer but can have multiple readers. This file is accessible to all the nodes in the system, regardless of their creation time.

### Logs
The `logs` folder contains logs, with each server having its own dedicated log. Every RPC call is tracked on these log files along with the timestamps of the execution of said calls.

### Other Functions
* `Hash`: calculate the hash value in a given range.
* `yaml_data_retriever`: retrive the token data for a specific token from the YAML file.
* `is_exists`: check if a token already exists or not.
* `print_all_tokens`: prints token IDs of all tokens except a specified token.
* `print_current_token`: prints complete information of a specified token.
* `fail_silent_check`: function to determine whether the fail-silent emulation conditions are satisfied.
* `get_port_list`: get the list of reader ports.
* `get_finalvals`: dispatch a read-impose write-majority RPC call and collect the final values and corresponding timestamps.
<br><br>

## Results
-----
To test the code, a simple bash file is provided. Executing that file will run the complete code with multiple scenarios already hardcoded into the bash file. Tokens are created, read, written, dropped, and a scenario to enforce fail-silent behavior is also provided. The bash file execution looks like this:

<div class="row">
	<div class="col-sm mt-3 mt-md-0">
		{% include figure.html path="assets/img/token_mgmt/outputs_client_command_sh.png" title="Command Execution" class="img-fluid rounded z-depth-1" %}
	</div>
</div>
<div class="caption">
	Command Execution
</div>


The server results are not printed onto the console. Instead, a log file for each server is created, to which the results and the activities of the servers are written into. For example, the log for the server running on the port 65000 shows the creation of the token 1020, with token state information being updated with the Write call. However, after the 10 second deadline for the fail-silent condition was met, the server starts to be unresponsive for the token 1020, which can be seen in the log.

<div class="row">
	<div class="col-sm mt-3 mt-md-0">
		{% include figure.html path="assets/img/token_mgmt/server_65000_log.png" title="Log of server running on port 65000" class="img-fluid rounded z-depth-1" %}
	</div>
</div>
<div class="caption">
	Log of server running on port 65000
</div>
