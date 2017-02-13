[![Code Climate](https://codeclimate.com/github/intracom-telecom-sdn/nstat-sdn-controllers/badges/gpa.svg)](https://codeclimate.com/github/intracom-telecom-sdn/nstat-sdn-controllers)
[![Documentation Status](https://readthedocs.org/projects/nstat-sdn-controller-handlers/badge/?version=latest)](http://nstat-sdn-controller-handlers.readthedocs.io/en/latest/?badge=latest)
[![Build Status](https://travis-ci.org/intracom-telecom-sdn/nstat-sdn-controllers.svg?branch=master)](https://travis-ci.org/intracom-telecom-sdn/nstat-sdn-controllers)
[![Docker Automated build](https://img.shields.io/docker/automated/jrottenberg/ffmpeg.svg?maxAge=2592000)](https://hub.docker.com/r/intracom/nstat-sdn-controllers/)
[![Issue Count](https://codeclimate.com/github/intracom-telecom-sdn/nstat-sdn-controllers/badges/issue_count.svg)](https://codeclimate.com/github/intracom-telecom-sdn/nstat-sdn-controllers)
[![Code Health](https://landscape.io/github/intracom-telecom-sdn/nstat-sdn-controllers/master/landscape.svg?style=flat)](https://landscape.io/github/intracom-telecom-sdn/nstat-sdn-controllers/master)
[![Codacy Badge](https://api.codacy.com/project/badge/Grade/5cf80fa926c046b4bdeaef817ff51756)](https://www.codacy.com/app/kostis-g-papadopoulos/nstat-sdn-controllers?utm_source=github.com&amp;utm_medium=referral&amp;utm_content=intracom-telecom-sdn/nstat-sdn-controllers&amp;utm_campaign=Badge_Grade)


# NSTAT SDN Controller Handlers

NSTAT SDN controller handlers are autonomous scripts which perform certain
[operations](https://github.com/intracom-telecom-sdn/nstat-sdn-controllers/tree/release#controller-handling-features)
required by NSTAT. We currently support controller handlers for the following
versions of OpenDaylight

*  [OpenDaylight Boron SR2](https://nexus.opendaylight.org/content/groups/public/org/opendaylight/integration/distribution-karaf/0.5.2-Boron-SR2/distribution-karaf-0.5.2-Boron-SR2.zip) controller
*  [OpenDaylight Boron](https://nexus.opendaylight.org/content/groups/public/org/opendaylight/integration/distribution-karaf/0.5.0-Boron/distribution-karaf-0.5.0-Boron.zip) controller
*  [OpenDaylight Beryllium SR3](https://nexus.opendaylight.org/content/repositories/public/org/opendaylight/integration/distribution-karaf/0.4.3-Beryllium-SR3/distribution-karaf-0.4.3-Beryllium-SR3.zip) controller
*  [OpenDaylight Beryllium](https://nexus.opendaylight.org/content/groups/public/org/opendaylight/integration/distribution-karaf/0.4.0-Beryllium/distribution-karaf-0.4.0-Beryllium.zip) controller
*  [OpenDaylight Lithium SR3](https://nexus.opendaylight.org/content/groups/public/org/opendaylight/integration/distribution-karaf/0.3.3-Lithium-SR3/distribution-karaf-0.3.3-Lithium-SR3.zip) controller
*  [OpenDaylight Lithium SR2](https://nexus.opendaylight.org/content/groups/public/org/opendaylight/integration/distribution-karaf/0.3.2-Lithium-SR2/distribution-karaf-0.3.2-Lithium-SR2.zip) controller
*  [OpenDaylight Lithium SR1](https://nexus.opendaylight.org/content/groups/public/org/opendaylight/integration/distribution-karaf/0.3.1-Lithium-SR1/distribution-karaf-0.3.1-Lithium-SR1.zip) controller
*  [OpenDaylight Helium SR3](https://nexus.opendaylight.org/content/groups/public/org/opendaylight/integration/distribution-karaf/0.2.3-Helium-SR3/distribution-karaf-0.2.3-Helium-SR3.zip) controller

However, similar handlers for other SDN controllers are about to be introduced
in future releases of this repository.

## Controller node deployment

Controller handlers can be directly executed on your local machine provided
that necessary tools/libraries, prerequisite for the ```*.sh```, ```*.py```
handler files are properly installed. However. for keeping your local machine
clean, a container based testing environment is provided or can be built as
desrcibed below.

The container based testing environment provided, can either be downloaded
directly from
[dockerhub](https://hub.docker.com/r/intracom/nstat-sdn-controllers/)
or built from the tools available within the ```/deploy``` folder.

In both cases, the [docker](https://www.docker.com/) platform must be installed
on the host side.

- essential tool: [docker](https://docs.docker.com/engine/installation/) (v.1.12.1 or later)

and the actions below have to be followed

- Give non-root access to docker daemon
    * Add the docker group if it doesn't already exist sudo groupadd docker
    * Add the connected user "${USER}" to the docker group. Change the user name to
match your preferred user
    ```bash
    sudo gpasswd -a ${USER} docker
    ```
    * Restart the Docker daemon:
    ```bash
    sudo service docker restart
    ```

### Download the pre-built enviroment

Once docker platform is installed, the user can pull the pre-built enviroment as
follows

```bash
docker pull intracom/nstat-sdn-controllers
```
Once the image pull operation is complete, check locally the existence of the
image

```bash
docker images
```
which should list the ``intracom/nstat-sdn-controllers:latest`` image.  For
running a container out of the ```intracom/nstat-sdn-controllers:latest``` image

```
docker run -it intracom/nstat-sdn-controllers /bin/bash
```

``password: root123``

Make a git clone of the nstat-sdn-controllers repository within the container

```
git clone -b v.1.0 https://github.com/intracom-telecom-sdn/nstat-sdn-controllers.git
```

and start testing all available controller handlers which are present under
```<PROJECT_DIR>/controllers``` directory


### Build your own container based testing environment


Once the docker platform is installed on the host machine, the user can build
locally the testing enviroment as follows

Make a git clone of the nstat-sdn-controllers repository within the host machine

```
git clone -b v.1.0 https://github.com/intracom-telecom-sdn/nstat-sdn-controllers.git
```

Navigate to

```<PROJECT_DIR>/deploy/docker/proxy``` directory. Enter the command below to
start building locally your own image based on the existing ``Dockerfile``.

```bash
docker build -t intracom/nstat-sdn-controllers:latest .
```

Verify that the image exists locally

```bash
docker images
```
should list ``intracom/nstat-sdn-controllers:latest`` image. Start your own
containeer as mentioned in the previous section.


## Controller handling features

Currently the supported controller is OpenDaylight. There is a common API in
the controller handling logic, followed by all versions of this controller.
Next, we describe this common API logic for the controller handlers.

1. **``change_persistence.py``**:
    - expected behaviour: handler disabling the persistence mode in the configuration
    of the controller. Changes the persistence attribute to false which is
    present at the ```org.opendaylight.controller.cluster.datastore.cfg``` of
    OpenDaylight. In this case the controller will not backup datastore on the
    disk. Run the ``./get_controller.sh`` handler before running this handler
    - prereqs: should be executable
    - input arguments: none
    - exit status:  `0` in case of success, `1` otherwise
    - usage example: ``python3.4 change_persistence.py``
1. **``change_stats_period.py``**:
    - expected behaviour: changes controller statistics interval. Run the
    ``./get_controller.sh`` handler before running this handler.
    - prereqs: should be executable
    - input arguments: statistic period (in ms)
    - exit status: not checked
    - usage example: ``python3.4 change_stats_period.py 500``
1. **``flowmods_configure.py``**:
    - expected behaviour: configures OpenDaylight controller for flow
    modifications. It currently changes controller configuration files in order to
    achieve mac-to-mac flow installation for Packet_INs with ARP payload. Run
    the ``./get_controller.sh`` handler before running this handler
      - `<controller base dir>/etc/opendaylight/karaf/54-arphandler.xml`:
      changes the value of key `is-proactive-flood-mode` from `true` to `false`.
      - `<controller base dir>/etc/opendaylight/karaf/58-l2switchmain.xml`:
      changes the values of keys `reactive-flow-idle-timeout` and
      `reactive-flow-hard-timeout` to 1. Run the ``./get_controller.sh`` handler
      before running this handler.
    - prereqs: should be executable
    - input arguments: no input arguments required
    - exit status: not checked
    - usage example: ``python3.4 flowmods_configure.py`
1. **``get_controller.sh``**:
    - expected behaviour: gets the pre-built specified version of the OpenDaylight
    controller.
    - prereqs: should be executable
    - input arguments: none
    - exit status: `0` in case of success, `non-zero` otherwise
    - usage example: ./get_controller.sh
1. **``get_flows.py``**:
    - expected behaviour: returns the number of installed flows of a topology,
    connected to the controller. This information is extracted from the
    controller's operational datastore, using RESTCONF. Run the ``./get_controller.sh``
    handler before running this handler.
    - prereqs: should be executable
    - input arguments: controller IP address in <str> form, controller restconf
    port number in <int> form, username for restconf authorization in <str> form
    password for restconf authorization in <str> form
    - exit status: not checked
    - usage example: ``python3.4 get_flows.py '127.0.0.1' '8181' 'admin' 'admin' ``
1. **``get_hosts.py``**:
    - expected behaviour: returns the number of hosts of a topology,
    connected to the controller. This information is extracted from the
    controller's operational datastore, using RESTCONF. Run the
    ``./get_controller.sh`` handler before running this handler.
    - prereqs:
    - input arguments: controller IP address in <str> form, controller restconf
    port number in <int> form, username for restconf authorization in <str> form
    password for restconf authorization in <str> form
    - exit status: not checked
    - usage example: ``python3.4 get_flows.py '127.0.0.1' '8181' 'admin' 'admin' ``
1. **``get_links.py``**:
    - expected behaviour: returns the number of links of a topology,
    connected to the controller. This information is extracted from the
    controller's datastore, using RESTCONF. Run the ``./get_controller.sh``
    handler before running this handler.
    - prereqs:
    - input arguments: controller IP address in <str> form, controller restconf
    port number in <int> form, username for restconf authorization in <str> form
    password for restconf authorization in <str> form
    - exit status: not checked
    - usage example: ``python3.4 get_flows.py '127.0.0.1' '8181' 'admin' 'admin' ``
1. **``get_switches.py``**:
    - expected behaviour: returns the number of switches of a topology,
    connected to the controller. This information is extracted from the
    controller's datastore, using RESTCONF. Run the ``./get_controller.sh`` handler
      before running this handler.
    - prereqs:
    - input arguments: controller IP address in <str> form, controller restconf
    port number in <int> form, username for restconf authorization in <str> form
    password for restconf authorization in <str> form
    - exit status:
    - usage example: ``python3.4 get_flows.py '127.0.0.1' '8181' 'admin' 'admin' ``
1. **``start.sh``**:
    - expected behaviour: starts the controller so that a switch can connect to
      it. Run the ``./get_controller.sh`` handler before running this handler.
    - prereqs: should be executable
    - input arguments: none
    - exit status: `0` in case of success, `non-zero` otherwise
    - usage example: ./start.sh
1. **``status.sh``**:
    - expected behaviour: queries the controller status. Run the ``./get_controller.sh`` handler
      before running this handler.
    - prereqs: should be executable
    - input arguments: none
    - output: `1` if controller is running, `0` otherwise
    - exit status: not checked
    - usage example: ./status.sh
1. **``stop.sh``**:
    - expected behaviour: stops the controller so that no switch can connect to
      it. Run the ``./get_controller.sh`` handler before running this handler.
    - prereqs: should be executable
    - input arguments: none
    - exit status: `0` in case of success, `non-zero` otherwise
    - usage example: ./stop.sh

## Controller handlers usage


## Adding your own handlers






