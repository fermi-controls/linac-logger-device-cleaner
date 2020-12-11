# Linac Logger Device Cleaner

This project was used to produce a list of valid and unique devices that are being data logged.

## Requirements

ACL is required to get the rates from the data logger lists associated with the devices.

## Workflow

Note: We don't have a method for getting the initial list of devices in the [`./data_logger_device_dump`](./data_logger_device_dump) directory. We should find a way to create these files for posterity.

Running `python3 parse_data_logger_devices.py` uses the files in the [`./data_logger_device_dump`](./data_logger_device_dump) directory to produce [`./output/linac_logger_unique_devices.txt`](./output/linac_logger_unique_devices.txt).

The output from above can be used as a device list with `acl linac_logger_lists.acl > output/linac_logger_rates.txt` to produce [`./output/linac_logger_rates.txt`](./output/linac_logger_rates.txt).
