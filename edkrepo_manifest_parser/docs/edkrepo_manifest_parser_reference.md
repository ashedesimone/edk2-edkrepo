
## Class: ManifestXml

### Description
The `ManifestXml` class represents a manifest file parser used for parsing and manipulating project manifest files consumed by EdkRepo. It provides methods to parse, access, and modify various attributes of the manifest, and generate new manifest and pin files. An instance of `ManifestXml` can represent either a *PinFile* or a full *ManifestFile*. This class is derived from the `BaseXmlHelper` class.

The manifest `ManifestXml` class does not validate the contents of manifest files for correctness beyond adherence with the project manifest file schema.

Per Pep8 methods and attributes beginning with `_` or `__` should be considered private and not directly invoked by consumers of this module. 

### Attributes
- `_project_info`: Stores the contents of the *ProjectInfo* subroot.
- `_general_config`: Stores the contents of the *GeneralConfig* subroot.
- `_remotes`: Stores the contents of the *RemoteList* subroot as a dictionary with the remote name as the key. Each unique *Remote* subtree is a separate entry.
- `_client_hook_list`: Stores the contents of the *ClientGitHookList* subroot.
- `_combinations`: Stores the contents of the *CombinationList* subroot as a dictionary with the combination name as the key. Each unique *Combination* subtree is a separate entry.
- `_combo_sources`: Lists of the *RepoSource* objects used by each combination stored as a dictionary with the combination name as the key. Each unique *Combination* is a separate entry.
- `_dsc_list`: Stores the contents of the *DscList* subroot as a list.
- `_sparse_settings`: Stores project level sparse checkout settings
- `_sparse_data`: Stores sparse checkout data.
- `_commit_templates`: Stores the contents of the *CommitTemplate* subroot as a dictionary with the applicable remote name as the key. Each unique *Template* is a separate entry.
- `_folder_to_folder_mappings`: Stores the contents of the *FolderToFolderMapping* subroot as a list of unique *FolderToFolderMapping* objects.
- `_submodule_alternate_remotes`: Stores the contents of the *SubmoduleAlternateRemote* subroot as a list of unique *SubmoduleAlternateRemote* objects.
- `_submodule_init_list`: Stores the contents of the *SelectiveSubmoduleInitList* subroot as a list of unique *SubmoduleInit* objects based upon each *Submodule* entry.
- `_patch_sets`: Stores each *PatchSet* defined in the *PatchSets* subroot as a list of tuples of the format: *(name, remote)*
- `_patch_set_operations`: Stores all operations required to process a given patch set as a dictionary where the key is a tuple of the format *(name, remote)* and each entry is a list of *PatchSetOperations* objects.

### Methods
- `__init__(self, fileref)`: Initializes the `ManifestXml` object with a file reference, verifies that custom syntax requirements (e.g. required fields) are met, appends included file references, and populates the class attributes. 
- `is_pin_file(self)`: Determines if a *Pin* or *Manifest* file is represented. Returns a boolean.
- `add_combo(self, element)`: Utility method which adds a new *Combination* to the manifest file.
- `_add_combo_source(self, subroot, combo)`: Parses the given *subroot* generating an appending *RepoSource* objects to `_combinations` for the given *combo*. 
  
  *Note: combo must match the name of an existing Combination listed in `_combinations`*
- `_add_unique_item(self, obj, item_dict, tag)`: Adds a key/value pair to the dictionary if and only if the key is not already present. Raises *KeyError* if a duplicate key is used.
- `_tuple_list(self, obj_list)`: Utility function which iterates through a list of parser objects returning a a list of *object.tuple()*
- `write_current_combo(self, combo_name, filename=None)`: Updates the *CurrentClonedCombo* tag and writes the entire tree to the file specified; if no file is specified then the one used to instantiate the *ManifestXml* object will be used. This method is not supported if the *ManifestXml* object refers to a *pin file*.
  
  *Note: this method will strip comments from the source file*
- `write_source_manifest_repo(self, manifest_repo, filename=None)`: Updates or adds the *SourceManifestRepository* and writes then entire tree to the file specified, if no file is specified then the one used to instantiate the *ManifestXml* object will be used. 

  [!Note]
  This method will strip comments from the source file
- `write_tree(self, filename=None)`: Writes the tree representing the entire *ManifestXml* object to the provided file.
- `generate_pin_xml(self, description, combo_name, repo_source_list, filename=None)`: Generates and writes an XML formatted *Pin* file containing only the provided `combo_name` and `repo_source_list` using the *ManifestXml* object.
- `generate_pin_json(self, description, combo_name, repo_source_list, filename=None)`: Generates and writes a JSON formatted *Pin* file containing only the provided `combo_name` and `repo_source_list` using the *ManifestXml* object.
- `equals(self, other, ignore_current_combo=False)`: Determines whether two *ManifestXml* objects reference the same project. If `ignore_current_combo=True` the *CurrentClonedCombo* tag will be ignored for the purpose of comparison.
- `get_patchset(self, name, remote)`: Looks up and returns a *PatchSet* object from `_patch_sets`. If no entry for the *(name,remote)* tuple exists *KeyError* is raised.
- `get_patchsets_for_combo(self, combo=None)`: Looks up and returns all *PatchSet* objects for the given *combo*. If *combo* is not specified provides all *PatchSet* objects for all combos present in the *ManifestXml* object. The data is returned as a dictionary where the key is the combo name. Raises *KeyError* if not *PatchSet* objects are identified. 
- `get_parent_patchset_operations(self, name, remote, patch_set_operations)`: Recursively identifies parent *PatchSetOperations* in `_patchsets` and appends them to the *patch_set_operations* parameter. If the identified *PatchsetOperation* is affiliated with a different *remote* name ValueError is raised. 
- `get_patchset_operations(self, name, remote)`: Returns a list of *PatchSetOperation* objects for the provided *name* and *remote*. Raises *ValueError* if not matching entry can be found in `_patch_sets` or if a *RecursionError* is caught.
- `get_repo_sources(self, combo_name)`: Returns a list of *RepoSource* objects for the given *combo_name*; raises *ValueError* if an invalid *combo_name* is provided. 
- `get_submodule_init_paths(self, remote_name=None, combo=None)`: Returns a list of *SubmoduleInit* objects.
- `get_submodule_alternates_for_remote(self, remote_name)`: Returns all *Submodule_Alternate_Remote* objects for the applicable *remote_name*. If no *Subdmodule_Alternate_Remotes* are present an empty list is returned.
- `_dfs_traverse_etree(self, node)`: Traverses the element tree returning a dictionary representation that can be used to create XML or JSON formatted *Pin* file.
- `generate_pin_etree(self, description, combo_name_repo_source_list)`: Returns an *etree* object representing a *Pin* file. Only the named combo is included.
- `_compare_elements(self, element1, element2)`: Recursively compares *element1* and *element2* returning a boolean.
- `__eq__(self, other)`: 
- `__ne__(self, other)`: 
- `get_combo_element(self, name)`: Finds the the combo element which matches *name* and returns a copy. Raises *ValueError* if no matching combo is found. 
### Properties
- `get_all_patchsets(self)`: Returns a list of all patch sets.
