# Masking at ingestion time

Masking and obfuscation can be very useful to hide specific content.

In the JSON file in this folder, you can find a Data Collection Rule that contains a *transformationKql* which performs two different kinds of masking. Below we break the query down into the two different kinds.

## Masking last 4 digits of Social Security Number

The first masking is done on a field called *SSN* which is supposed to contain a Social Security Number. The goal is to replace the last 4 numbers in the SSN with Xs. This is the part of the transformation that does that:

```csharp
source 
| where SSN matches regex @'^\\d{3}-\\d{2}-\\d{4}$' 
and not( SSN matches regex @'^(000|666|9)-\\d{2}-\\d{4}$') 
and not( SSN matches regex @'^\\d{3}-00-\\d{4}$') 
and not (SSN matches regex @'^\\d{3}-\\d{2}-0000$' ) 
| extend parsedSSN = split(SSN,'-') 
| extend SSN = strcat(parsedSSN[0],'-', parsedSSN[1],'-','XXXX') 
| project-away parsedSSN
```

As you can see, we use ```matches regex``` to find the pattern of a Social Security Number (including exclusions). Then we use ```split``` operator to break down the SSN field in the three number blocks and lastly we rearrange the blocks replacing the last one with Xs. Finally, we remove the intermediary variable.

## Removing Personal Identifiable Information

This second masking will completely replace a field that contains an email address. Here is the transformation:

```csharp
source 
| where Email matches regex "^[a-zA-Z0-9+_.-]+@[a-zA-Z0-9.-]+$" 
| extend Email = replace_string(Email,tostring(Email),'PII removed')
```

This one is easier, because we just need the right regex for email addresses and then we replace the whole field.