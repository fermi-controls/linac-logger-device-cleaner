# Linac Logger Device Cleaner

This project was used to produce a list of valid and unique devices that are being data logged.

## Requirements

ACL is required to get the rates from the data logger lists associated with the devices.

## Workflow

### Generate a list of Linac data logger devices

[`./linac_logger_devices.acl`](./linac_logger_devices.acl) generates five files. A file for each Linac logger node, in the format `./output/linac_logger_devices_<logger-name>.txt`, and a file with all the devices from all the nodes, [`./output/linac_logger_devices.txt`](./output/linac_logger_devices.txt).

### Find unique devices and validate them

Executing `python parse_data_logger_devices.py` generates two files [`./output/linac_logger_unique_devices.txt`](./output/linac_logger_unique_devices.txt) and [`./output/linac_logger_valid_devices.txt`](./output/linac_logger_valid_devices.txt).

The validation step uses regular expressions to match the standard device database naming convention.

### Generate a list of Linac data logger requests

Using acl [`./linac_logger_lists.acl`](./linac_logger_lists.acl) we can query the data loggers for the request rates of Linac logger devices. This script produces [`./output/linac_logger_rates.txt`](./output/linac_logger_rates.txt).

The output from above is used to generate [DRF](https://www-bd.fnal.gov/controls/public/drf2/) requests for the persistent ML data pipeline using [`./parse_acl_logger_rates.py`](./parse_acl_logger_rates.py).

---

Note: We don't have a method for getting the initial list of devices in the [`./data_logger_device_dump`](./data_logger_device_dump) directory. We should find a way to create these files for posterity.

Running `python3 parse_data_logger_devices.py` uses the files in the [`./data_logger_device_dump`](./data_logger_device_dump) directory to produce [`./output/linac_logger_unique_devices.txt`](./output/linac_logger_unique_devices.txt).

The output from above can be used as a device list with `acl linac_logger_lists.acl > output/linac_logger_rates.txt` to produce [`./output/linac_logger_rates.txt`](./output/linac_logger_rates.txt).

### Duplicate device count

[`./output/linac_logger_duplicate_count.txt`](./output/linac_logger_duplicate_count.txt) was generated by running `sort output/linac_logger_devices.txt | uniq -c | sort > output/linac_logger_duplicate_count.txt` from a unix command line.

## Validating device lists

There's concern that we don't have a comprehensive list of relevant devices given that most sources of devices are human maintained. Here are strategies for determining what devices may be left out.

### Device database machine field

The device database has a machine field that can be Linac. The output files are a list of devices with the machine field Linac, [`./output/machine_linac_devices.txt`](./output/machine_linac_devices.txt). Then there are files for the devices of that list that are logged, [`./output/logged_linac_devices.txt`](./output/logged_linac_devices.txt), and not logged, [`./output/unlogged_linac_devices.txt`](./output/unlogged_linac_devices.txt). These lists are generated by [`./gen_device_lists.acl`](./gen_device_lists.acl).

Note: Queries to the data logger configuration are slow so it takes minutes to run the ACL script.

### Node database area field

The front-end node database has an area field that can be Linac. [`./gen_linac_area_nodes.acl`](./gen_linac_area_nodes.acl) generates a list of nodes where area is Linac, [`./output/area_linac_nodes.txt`](./output/area_linac_nodes.txt), and lists of devices associated with those nodes, [`./output/node_devices/`](./output/node_devices/).

Those resulting files can be combined using `cat output/node_devices/* > output/area_linac_nodes_devices.txt` resulting in [`./output/area_linac_nodes_devices.txt`](./output/area_linac_nodes_devices.txt).

`grep -vi '^Z' output/area_linac_nodes_devices.txt > output/area_linac_nodes_devices_no_z.txt` will remove any `Z:%` devices from the list of devices resulting in [`./output/area_linac_nodes_devices_no_z.txt`](./output/area_linac_nodes_devices_no_z.txt).

[`./validate_devices.acl`](./validate_devices.acl) is then used to validate that the devices in [`./output/area_linac_nodes_devices_no_z.txt`](./output/area_linac_nodes_devices_no_z.txt) aren't deleted or obsolete, producing [`./output/area_linac_nodes_devices_valid.txt`](./output/area_linac_nodes_devices_valid.txt).

### Strategy comparison

The machine field strategy results in 8532 devices.

The area field strategy results in 9057 devices.

## TODOs

### Questions to answer

- What and how many are all the Linac devices?
- What and how many Linac devices are logged?
  - [`./output/linac_logger_drf_requests.txt`](./output/linac_logger_drf_requests.txt)

### Strategies to validate

- `L:%`
- Node database area field
- Node database system field
- Device database machine field
- Data logger databases devices
