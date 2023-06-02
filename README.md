# Migrate Secrets Azure KeyVault


```bash

Source_Kv_Name="kv-source"
Dest_Kv_Name="kv-dest"
SECRETS+=($(az keyvault secret list --vault-name $Source_Kv_Name --query "[].id" -o tsv))
for SECRET in "${SECRETS[@]}"; do
SECRETNAME=$(echo "$SECRET" | sed 's|.*/||')
SECRET_CHECK=$(az keyvault secret list --vault-name $Dest_Kv_Name --query "[?name=='$SECRETNAME']" -o tsv)
if [ -n "$SECRET_CHECK" ]
then
    echo "$SECRETNAME already exists in $Dest_Kv_Name"
else
     echo "Copying $SECRETNAME from Source KeyVault: $Source_Kv_Name to Destination KeyVault: $Dest_Kv_Name"
    SECRET=$(az keyvault secret show --vault-name $Source_Kv_Name -n $SECRETNAME --query "value" -o tsv)
    az keyvault secret set --vault-name $Dest_Kv_Name -n $SECRETNAME --value "$SECRET" >/dev/null
fi
done


```

