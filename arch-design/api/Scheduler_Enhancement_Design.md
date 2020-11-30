# Scheduler enhancement design

Author(s): PengYiOfOpenSDS

## Summary

This proposal is drafted to enhance the scheduler of OpenSDS.

## Motivation

In the current version of OpenSDS, the user can set "extras" in the profile to select a pool when creating the volume. However, the current method of filtering the pool cannot meet the complex needs of users, so OpenSDS need to enhance the filtering method.

### Goals

* Design a new framework for filtering pools.

### Non-Goals

The proposal only satisfies the data model of the pool and profile in the current OpenSDS. If the data model of the pool or profile has changed in the future, OpenSDS need to modify the proposal again.

## Proposal

This proposal is drafted to enhance the scheduler of OpenSDS.

1. OpenSDS convert "pool" from a StoragePoolSpec to a map[string]interface{}. 

For example: 

```
   PoolA = StoragePoolSpec{
		AvailabilityZone: "az1",
		Extras: model.StoragePoolExtraSpec{
			Advanced: model.ExtraSpec{
				"thin": true,
			},
		}
        ...
	}
```

The result is:

```
    PoolB = map[string]interface{
			"availabilityZone": "az1", // "availabilityZone" = The json name corresponding to "AvailabilityZone".
			"extras.advanced.thin": true, // "extras.thin" = The json name corresponding to "Extras" + The json name corresponding to "Advanced" + "." + The original name of "thin"
            ...
	}
```
    
2. OpenSDS uses the `extras` in the profile as a filter to filter out a pool when user creates a volume.

   `extras` in profile is `Request_key: Request_value`.
   `pool` is `Pool_key: Pool_value`.
   Request_value may be a string, and may specify an operator, such as: `s== az1`, where `s==` is an operator and `az1` is an operation value. The operation follows the operator value with a space as a delimiter.
   
   * No operator

   If a pool has the ability `Request_key`, and Request_value is equal to the Pool_value, the pool will be selected.
   If the actual type of Request_value is not of string type, OpenSDS will convert Request_value from interface{} 
   to the same type as Pool_value with a type assertion, and then compare. If the actual type of Request_value is string 
   and the actual type of Pool_value is bool, OpenSDS will convert Request_value with strconv.ParseBool(). If the actual 
   type of Request_value is string and the actual type of Pool_value is float64, OpenSDS will use strconv.ParseFloat().
   
   * `<=, >=, ==, !=`

   This does a float64 conversion and then uses the operators as expected. If operator is `==`, epsilon is 0.00000001.
   
   * `<in>`

   OpenSDS will use regexp.MatchString(operation value, Pool_value) to determine if the pool's capabilities meet the 
   filter criteria. For example, if `availabilityZone: <in> az1` is used, it will match a pool that reports capability of `az1_1` or `az1_2`.
   
   * `<or>`

   This chooses a pool that has one of the items specified. If the first word in the string is `<or>`, another `<or>` and value pair can be concatenated. Examples are `<or> 3`, `<or> 3 <or> 5`, and `<or> 1 <or> 3 <or> 7`.
   
   * `<is>`

   This chooses a pool that matches a boolean capability. An example `extras` value would be `<is> true`.
   
   * `s==, s!=, s>=, s>, s<=, s<`

   The `s` indicates it is a string comparison. These choose a pool that satisfies the comparison of strings in capability and specification. For example, if `availabilityZone`: `s== az1`, a pool that reports availabilityZone of `az1` will be selected.

### Data model impact

None

### REST API impact

None

### Security impact

None

### Other end user impact

None

### Performance impact

None

### Other deployer impact

None

### Developer impact

None

## Use Cases

If the user creates a volume and requires the volume's AvailabilityZone to be az1, then the user can set the `extras["availabilityZone"]` in the profile to `az1` or `s== az1` before creating the volume.

## Alternatives considered

None
