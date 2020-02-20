# Azure SDK for C Logging

For the V2 SDK we will be leveraging the [Azure SDK for C](https://github.com/Azure/azure-sdk-for-c) logging implementation.

## High Level Architecture

Essentially the various components of the SDK are broken up into "classifications". Log messages are filtered with these classification enum's so that the user can decide which log messages they want. At the beginning of the user's code, they can initialize the logging by optionally setting the classifications they want, setting their logging listener, and then logging any messages they desire. 

### Basic Code Snippet

This is how we would be integrating in with the Azure SDK for C log code:

```c
/* az_facility.h */
enum {
  AZ_FACILITY_CORE = 0x1,
  AZ_FACILITY_JSON = 0x2,
  AZ_FACILITY_HTTP = 0x3,
  AZ_FACILITY_IOT  = 0x4,
  AZ_FACILITY_STD = 0x7FFF,
};

/* az_log.h */
typedef enum {
  AZ_LOG_HTTP_REQUEST  = _az_LOG_MAKE_CLASSIFICATION(AZ_FACILITY_HTTP, 1),
  AZ_LOG_HTTP_RESPONSE = _az_LOG_MAKE_CLASSIFICATION(AZ_FACILITY_HTTP, 2),
  AZ_LOG_IOT           = _az_LOG_MAKE_CLASSIFICATION(AZ_FACILITY_IOT, 1),
} az_log_classification;
```



```c
az_log_classification const classifications[] = { AZ_LOG_HTTP_REQUEST, AZ_LOG_IOT };
static az_span test_log_message = AZ_SPAN_LITERAL_FROM_STR("test log");

void test_log_func(az_log_classification classification, az_span message)
{
    printf("/.*s", az_span_length(message), az_span_ptr(message));
}

void set_logging(void)
{
    az_log_set_classifications(classifications, 
           sizeof(classifications)/sizeof(classifications[0]));
    az_log_set_listener(&test_log_func);
    
    az_log_write(AZ_LOG_IOT, test_log_message);
}
```

Here the classifications are set to `AZ_LOG_HTTP_REQUEST` and `AZ_LOG_IOT`. Should the `AZ_LOG_IOT` be omitted from the set of classifications, the log message in `set_logging()` will not be logged.

If no classifications are set then all messages are logged.

## Necessary Work for IoT SDK V2

1. [DONE] We will need our own [facility code](https://github.com/Azure/azure-sdk-for-c/blob/1ed90fbe699c15cd308cd4c8dcd45c3e40205a31/sdk/core/core/inc/az_facility.h#L9-L14), likely `AZ_FACILITY_IOT`.
2. We will have to put in a [logging classification](https://github.com/Azure/azure-sdk-for-c/blob/1ed90fbe699c15cd308cd4c8dcd45c3e40205a31/sdk/core/core/inc/az_log.h#L19-L22), like `AZ_LOG_IOT`.

## Current Issues

- The buffers allocated on the stack will not play nice with RTOS threads on constrained devices (currently at 1024 size).
  - [Link to code](https://github.com/Azure/azure-sdk-for-c/blob/1ed90fbe699c15cd308cd4c8dcd45c3e40205a31/sdk/core/core/src/az_log.c#L174)
    - Could think about having as a configurable `#define`

## Links to Relevant Resources

- [az_log.h](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/core/core/inc/az_log.h)
- Main header file where user can set the log listener (user log function) and set the classifications (modules) that they wish to have logs for. 
- [az_log_internal.h](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/core/core/internal/az_log_internal.h)

  - Has the `az_log_write` primitive which is what is used to call the user's log function.
- [az_log_private.h](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/core/core/src/az_log_private.h)

  - Currently has http specific log utilities like logging requests and responses.
- [az_log.c](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/core/core/src/az_log.c)
  - Has all the implementations for logging purposes.