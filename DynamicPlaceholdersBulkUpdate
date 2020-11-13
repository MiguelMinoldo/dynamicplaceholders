function ProcessRendering{
    [CmdletBinding()]
    Param(
    [parameter(Mandatory=$true, Position=0)]
    [Sitecore.Layouts.RenderingDefinition]
    $rendering,
    [parameter(Mandatory=$false, Position=1)]
    [string]
    $param
 )
	$phMatches = [regex]::Matches($_.Placeholder,'(_[0-9a-fA-F]{8}[-][0-9a-fA-F]{4}[-][0-9a-fA-F]{4}[-][0-9a-fA-F]{4}[-][0-9a-fA-F]{12})')

	if ($phMatches.Success) {
		Write-Host "Match found in item - [$($item.Paths.FullPath)]"
		$replacePh = $rendering.Placeholder
		$matches | ForEach-Object {
			$renderingId = $_.Value
			$replacePh = $replacePh.Replace($renderingId, "{$($renderingId.ToUpper())}-0")
		}

		$replacePh = $replacePh.Replace('{_', "-{")
		$rendering.Placeholder = $replacePh
		Write-Host "Replacing [$($rendering.Placeholder)] with [$($replacePh)]"
		Set-Rendering -Item $item -Instance $rendering $param
	}
}

$allPaths = "/sitecore/templates", "/sitecore/content"
$finalLayoutParm = "-FinalLayout"
New-UsingBlock (New-Object Sitecore.Data.BulkUpdateContext) {
	$allPaths | ForEach-Object -Process {
		Get-ChildItem -Path $_ -Version * -Recurse | ForEach-Object {
			$item = $_;
			Write-Host "Processing shared layout for item $($_.ID)" 
			Get-Rendering -Item $_ | ForEach-Object {
				ProcessRendering $_
			}
			Write-Host "Processing final layout for item $($_.ID)" 
			Get-Rendering -Item $_ -FinalLayout | ForEach-Object {
				ProcessRendering $_ $finalLayoutParm
			}
		}
	}
}

