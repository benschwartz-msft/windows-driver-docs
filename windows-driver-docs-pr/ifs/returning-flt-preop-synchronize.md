---
title: Returning FLT_PREOP_SYNCHRONIZE
description: Returning FLT_PREOP_SYNCHRONIZE
keywords:
- FLT_PREOP_SYNCHRONIZE
- synchronization WDK file system minifilter
ms.date: 04/20/2017
---

# Returning FLT\_PREOP\_SYNCHRONIZE


## <span id="ddk_returning_flt_preop_synchronize_if"></span><span id="DDK_RETURNING_FLT_PREOP_SYNCHRONIZE_IF"></span>


If a minifilter driver's [**preoperation callback routine**](/windows-hardware/drivers/ddi/fltkernel/nc-fltkernel-pflt_pre_operation_callback) synchronizes an I/O operation by returning FLT\_PREOP\_SYNCHRONIZE, the filter manager calls the minifilter driver's [**postoperation callback routine**](/windows-hardware/drivers/ddi/fltkernel/nc-fltkernel-pflt_post_operation_callback) during I/O completion.

The filter manager calls the minifilter driver's postoperation callback routine in the same thread context as the preoperation callback, at IRQL &lt;= APC\_LEVEL. (Note that this thread context is not necessarily the context of the originating thread.)

**Note**   If the minifilter driver's preoperation callback routine returns FLT\_PREOP\_SYNCHRONIZE, but the minifilter driver has not registered a postoperation callback routine for the operation, the system asserts on a checked build.

 

If the minifilter driver's preoperation callback routine returns FLT\_PREOP\_SYNCHRONIZE, it can return a non-NULL value in its *CompletionContext* output parameter. This parameter is an optional context pointer that is passed to the corresponding postoperation callback routine. The postoperation callback routine receives this pointer in its *CompletionContext* input parameter.

A minifilter driver's preoperation callback routine should return FLT\_PREOP\_SYNCHRONIZE only for IRP-based I/O operations. However, this status value can be returned for other operation types. If it is returned for an I/O operation that is not an IRP-based I/O operation, the filter manager treats this return value as if it were FLT\_PREOP\_SUCCESS\_WITH\_CALLBACK. To determine whether an operation is an IRP-based I/O operation, use the [**FLT\_IS\_IRP\_OPERATION**](/previous-versions/ff544654(v=vs.85)) macro.

Minifilter drivers should not return FLT\_PREOP\_SYNCHRONIZE for create operations, because these operations are already synchronized by the filter manager. If a minifilter driver has registered preoperation and postoperation callback routines for IRP\_MJ\_CREATE operations, the post-create callback routine is called at IRQL = PASSIVE\_LEVEL, in the same thread context as the pre-create callback routine.

Minifilter drivers must never return FLT\_PREOP\_SYNCHRONIZE for asynchronous read or write operations. Doing so can severely degrade both minifilter driver and system performance and can even cause deadlocks if, for example, the modified page writer thread is blocked. Before returning FLT\_PREOP\_SYNCHRONIZE for an IRP-based read or write operation, a minifilter driver should verify that the operation is synchronous by calling [**FltIsOperationSynchronous**](/windows-hardware/drivers/ddi/fltkernel/nf-fltkernel-fltisoperationsynchronous).

The following types of I/O operations cannot be synchronized:

-   Oplock file system control (FSCTL) operations (**MajorFunction** is IRP\_MJ\_FILE\_SYSTEM\_CONTROL; **FsControlCode** is [**FSCTL\_REQUEST\_FILTER\_OPLOCK**](./fsctl-request-filter-oplock.md), [**FSCTL\_REQUEST\_BATCH\_OPLOCK**](./fsctl-request-batch-oplock.md), [**FSCTL\_REQUEST\_OPLOCK\_LEVEL\_1**](./fsctl-request-oplock-level-1.md), or [**FSCTL\_REQUEST\_OPLOCK\_LEVEL\_2**](./fsctl-request-oplock-level-2.md).)

-   Notify change directory operations (**MajorFunction** is IRP\_MJ\_DIRECTORY\_CONTROL; **MinorFunction** is IRP\_MN\_NOTIFY\_CHANGE\_DIRECTORY.)

-   Byte-range lock requests (**MajorFunction** is IRP\_MJ\_LOCK\_CONTROL; **MinorFunction** is IRP\_MN\_LOCK.)

FLT\_PREOP\_SYNCHRONIZE cannot be returned for any of these operations.

 

