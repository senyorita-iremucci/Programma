size_t video_files_len(const video_file_t** files) 
{ 
    if (files == NULL) 
        return 0;
    size_t len = 0; 
    for (const video_file_t** file = files; *file != NULL; file++) { 
        len++; 
    } 
    return len; 
} 

void video_files_swap(video_file_t* a, video_file_t* b, copy_func_t copy_func) 
{ 
    if (a == NULL || b == NULL) return;
    video_file_t tmp; 
    copy_func(&tmp, a); 
    copy_func(a, b); 
    copy_func(b, &tmp); 
} 

video_file_t** sort_video_files(const video_file_t** files, 
    is_less_func_t is_less_func, 
    copy_func_t copy_func, 
    malloc_func_t malloc_func) 
{ 
    size_t len = video_files_len(files); 

    if (len == 0) { 
        return NULL;
    } 

    video_file_t** sorted_files = malloc_func(sizeof(video_file_t*) * (len + 1)); 

    if (sorted_files == NULL) return NULL;

    for (size_t i = 0; i < len; i++) { 
        sorted_files[i] = malloc_func(sizeof(video_file_t)); 
        if (sorted_files[i] == NULL) {
            for (size_t j = 0; j < i; j++) {
                free(sorted_files[j]);
            }
            free(sorted_files);
            return NULL;
        }
        copy_func(sorted_files[i], files[i]); 
    } 

    for (size_t i = 0; i < len - 1; i++) { 
        size_t min_ind = i; 
        for (size_t j = i + 1; j < len; j++) { 
            if (is_less_func(sorted_files[j], sorted_files[min_ind])) { 
                min_ind = j; 
            } 
        } 
        if (min_ind != i) { 
            video_files_swap(sorted_files[i], sorted_files[min_ind], copy_func); 
        } 
    } 

    sorted_files[len] = NULL;

    return sorted_files; 
}
